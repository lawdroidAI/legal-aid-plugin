---
name: client-comms-log
description: >
  Log a client or prospective-client communication — call, email, text,
  letter, in-person, voicemail. Append-only per-case record with dated
  entries, direction, medium, summary, action items. Rule 1.18-aware for
  prospective clients (even declined matters); VAWA-aware for confidentiality
  with DV survivors. Works alongside /client-letter and /status client.
  Use when logging a call or client email, reviewing a communication log,
  or asking "what did we tell [client] last time".
argument-hint: "[case-id or screen-id] [--add (default) | --read | --summary | --patterns]"
---

# /client-comms-log

1. Load `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → funders, languages, confidentiality conventions.
2. Use the workflow below.
3. Require case-ID or screen-ID (prompt if not provided).
4. Route by flag:
   - `--add` (default): capture direction, medium, staffer, summary, action items, follow-up due. Confirm with user. Append (prepend most-recent-first) to `~/.claude/plugins/config/claude-for-legal/legal-aid/client-comms/[case-or-screen-id]/log.md`.
   - `--read`: show the most recent N entries.
   - `--summary`: one-paragraph condensed read.
   - `--patterns`: scan for unanswered comms, missed follow-ups, language gaps, tone shifts, contact gaps. Supervision-oriented.
5. Integration: offer `/legal-aid:deadlines --add` if the log establishes a deadline; route to `/legal-aid:case-transfer --summary` when relevant.

```
/legal-aid:client-comms-log case=marisol-2026-04
/legal-aid:client-comms-log case=marisol-2026-04 --read
/legal-aid:client-comms-log screen=screen-2026-447 --add
/legal-aid:client-comms-log case=jackson-2025-12 --patterns
```

---

# Client Communications Log

## Purpose

Four reasons to keep this log in civil legal aid practice:

1. **Malpractice defense.** If a client claims "no one ever told me [X]," a dated entry showing otherwise is the answer. Managing attorneys carry professional liability on staff and paralegal work; contemporaneous records protect them.
2. **Continuity at staff transition.** A paralegal leaves, a staff attorney returns from leave, an intake specialist hands the case to housing — they all read the log. They don't re-ask the client questions already answered.
3. **Supervision visibility.** Five unreturned voicemails over six weeks is a pattern. The log makes patterns visible that individual staff might not flag on their own.
4. **File retention.** Legal aid offices have funder and bar obligations to maintain complete client files. Communication history is part of that.

## Scope: clients and prospective clients

The log handles two case types:

- **Accepted matters** — full case ID, fully privileged
- **Screened-but-declined matters** — screen-ID, Rule 1.18-protected (prospective-client confidentiality applies even though the office didn't take the case)

Rule 1.18 means the office's obligations of confidentiality to a person who consulted about possible representation attach even if the office declined. Communications during eligibility screening and any subsequent contact with a declined caller belong in the screen-ID's log, not in a separate "we didn't take this" file. The log structure is identical; the directory differs (`client-comms/case-*` for accepted, `client-comms/screen-*` for declined).

## Special handling for VAWA-funded matters and DV survivors

Communications about VAWA-funded DV matters carry additional confidentiality requirements under 42 USC § 13925(b)(2). The log entries themselves are office-confidential as always; the distinct constraints are:

- The log is not shared with the abuser or anyone in the abuser's household, regardless of relationship to the office
- The log is not shared with co-counsel from outside the office without explicit client consent
- Funder reporting (`/lsc-report` for VAWA quarterlies) aggregates from the log without reproducing identifying details — small-N aggregation risks identification
- Even within the office, VAWA-matter comms are accessed on a need-to-know basis; the log notes this at the top of any DV-flagged matter

The skill applies these constraints automatically when the matter is flagged as VAWA-funded in the case file.

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → funders, languages served, confidentiality conventions, supervision style.

The matter's case file → matter status (accepted / declined), funder allocation, practice area, any DV / VAWA / heightened-confidentiality flag, prior log entries.

## Workflow

### `--add` (default) — Log a new communication

Ask:
- Direction: in (client to office) / out (office to client)
- Medium: phone call / voicemail (received) / voicemail (left) / email / text / letter (mail) / in-person / video meeting / other
- Date and time (default to now; allow override for backdating with a flag and reason)
- Staffer name (who took the call / sent the email)
- Client present? (relevant for in-person and video — sometimes client is with a support person, an interpreter, family)
- Language used (if not the office's default — flag if interpreter was present)
- Summary: 2-4 sentence factual summary. **Substantive legal analysis does not go here** — that lives in memos. This is the record of what was communicated.
- Action items (for office and/or for client) — each with optional deadline
- Follow-up due (if any) — concrete date

Before committing:
- Check whether the action items include a date that should be added to the deadline ledger. If yes, offer: "This communication established a deadline ([action] by [date]). Add to `/legal-aid:deadlines --add`?"
- Check for tone or content that suggests escalation — caller distressed, threat to safety mentioned, withdrawal from representation hinted, dispute about prior advice. If detected, note: "This entry contains content that may warrant managing-attorney attention. Flag for review?"

Append to the log. Confirm with the user before writing.

### `--read [N]`

Show the most recent N entries (default 10). Format:

```
## [Date timestamp] — [direction] · [medium] · [staffer]
[Language note if not default]
Summary: [2-4 sentences]
Action items: [list with deadlines if any]
Follow-up due: [date or "none"]
```

For a long log, the most recent entries are at the top. Scrolling backward is chronological-reverse (newest first).

### `--summary`

One-paragraph condensed read of the full log. Useful for:
- A staffer picking up a transferred case
- A managing attorney preparing for case rounds
- A funder monitoring request

The summary covers: communication frequency, who's been driving the contact (office or client), recent significant communications, any unresolved action items, any pattern flags. Not legal analysis — communication pattern.

### `--patterns`

Scan-the-log for supervision-relevant patterns:

- **Unanswered communications:** office voicemails left without response after [threshold] days
- **Missed follow-ups:** action items with deadlines past, not completed
- **Language gaps:** entries indicating language barrier where no interpreter was present
- **Tone shifts:** entries indicating client became upset, withdrew, or escalated
- **Contact gaps:** periods over [threshold] days with no recorded contact in an active matter (often the staffer made contact but didn't log; sometimes the matter actually went dark — both matter)
- **VAWA-matter access pattern:** if a VAWA-flagged matter shows access by staff who don't have a documented role in the matter, flag for managing-attorney review (this is rare and worth surfacing)

Output is supervision-oriented: what patterns exist, what they might mean, what the suggested follow-up is. The managing attorney decides what to do with the patterns.

## Output format (per entry in log file)

The log file at `client-comms/[case-or-screen-id]/log.md` accumulates entries newest-first:

```markdown
# Communications log: [case ID or screen ID]
**Client:** [name if accepted matter / "prospective client (Rule 1.18)" if screened-only]
**Practice area:** [from case file]
**Funder allocation:** [from case file; "screening only" if not accepted]
**Confidentiality flags:** [VAWA / DV / heightened / standard]

---

## [Date timestamp] · [direction in/out] · [medium] · [staffer]

[Optional: language used, interpreter present, client accompaniment]

**Summary:** [2-4 factual sentences]

**Action items:**
- [office or client] [action] [deadline if any]

**Follow-up due:** [date or "none"]

[Optional: flag for managing-attorney attention with reason]

---

[Next entry in same format]
```

## Cross-skill propagation

- Communications logged here that include a new deadline propagate to `/legal-aid:deadlines --add`.
- Communications logged here that include a new fact bearing on intake or eligibility (income changed, address changed, new conflict surfaced) prompt: "This entry contains information that may update the matter's intake. Re-run `/legal-aid:client-intake --update` to refresh?"
- `/legal-aid:case-transfer` pulls a `--summary` of the log into the transfer memo automatically.
- `/legal-aid:status client` and `/legal-aid:client-letter` invocations should append themselves to the log after sending (this happens via the office's workflow — the staffer runs `/legal-aid:client-comms-log --add` after sending the letter).

## What this skill does NOT do

- Store substantive legal analysis. Analysis goes in `/memo`; this log records the communication, not the lawyering behind it.
- Auto-summarize calls or emails from connected systems. The staffer summarizes what was said — they were there, the tool wasn't.
- Edit prior entries. Append-only. Corrections happen as new entries that reference the prior one ("Correction to entry of [date]: client name is X, not Y as previously recorded.").
- Delete entries. Office privilege and bar-retention obligations apply; entries persist.
- Override VAWA / Rule 1.18 confidentiality constraints. Treats VAWA-flagged matters with the additional confidentiality discipline; treats screen-IDs with Rule 1.18 attached.
- Share across cases. Each case ID's log lives in its own directory; no cross-case search unless the managing attorney specifically authorizes (rare, and surfaces in the audit log).
