---
name: case-transfer
description: >
  Per-case handoff memo for rolling staff transitions — departure, role change,
  leave coverage, intake-to-staff routing. Status snapshot, what's pending,
  what's next, deadlines, conflict re-check for the incoming attorney, open
  questions for the supervisor. Use when a case is moving between staff for any
  reason, when a staffer is going on leave, or when intake hands a matter to a
  staff attorney for the first time.
argument-hint: "[case-id] [from=staff] [to=staff] [reason=departure|leave|role-change|intake-handoff]"
---

# /case-transfer

1. Load `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → conflict-check process, supervision style.
2. Load the case file (or matter materials) for the case being transferred.
3. Use the workflow below.
4. Produce a transfer memo per the output template.
5. Save to `case-transfers/[YYYY-MM]/[case-id].md`.
6. If the office runs formal review, queue the transfer for managing-attorney sign-off; otherwise apply the configured flag.

```
/legal-aid:case-transfer case=jones-2026-04 from=alvarez to=tanaka reason=leave
```

---

# Case Transfer

## Purpose

Unlike a clinic that hands off everything at semester end, a legal aid office runs continuous transitions: a paralegal leaves, a staff attorney goes on parental leave, an attorney moves from intake to housing, a managing attorney reassigns a case for capacity reasons, an intake specialist hands a screened-and-accepted matter to the staff attorney who will carry it.

Each transition is a malpractice exposure point — deadlines get dropped, conflicts get re-engaged, client expectations get reset without the new attorney knowing what was promised. This skill produces a transfer memo that closes that exposure.

**What it doesn't do:** close the case. Cases closing get a final `/status internal` memo and are marked closed in the office's case management system. Use this skill only when the case is moving, not ending.

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → conflict-check process (the new attorney needs to clear), supervision style (whether the transfer queues for managing-attorney review), supervision flag triggers.

The case file itself: prior intake summary, eligibility screening result, any drafts in progress, status memos, communication log, deadline entries.

## Workflow

### Step 1: Identify the transfer

Capture the basics if not provided as arguments:
- Case ID
- From (departing or transferring staffer)
- To (incoming staffer; may be "unassigned — managing attorney to assign")
- Reason: departure / role change / leave coverage / intake-to-staff / capacity reassignment / other
- Effective date

If the case is `unassigned — to be assigned`, run the full transfer memo anyway. The managing attorney needs the same information whether assigning today or next week.

### Step 2: Pull the case status snapshot

From the case file:
- Practice area + cross-area issues
- Funding source(s) the matter is accepted under
- Eligibility flags from screening that are still live
- Current case posture (pre-filing / in litigation / post-judgment / pre-administrative-hearing / etc.)
- Open court or agency deadlines
- Open client deliverables (callbacks owed, documents promised, status updates committed)
- Open internal deliverables (drafts in progress, research outstanding)

Tag every item with its source — case file, communication log, deadline ledger, or staff recollection (which is the weakest source and needs explicit verification by the new attorney).

### Step 3: Re-run conflict check for the new attorney

The incoming attorney is a new lawyer for the matter, even if from the same office. Per office policy, that may or may not require a fresh conflict check at the individual level. Run whatever the office's CLAUDE.md describes; at minimum:

- Does the incoming attorney have any personal conflict with the parties involved?
- Has the incoming attorney previously represented or consulted with any party?
- Any positional conflict the incoming attorney's current caseload creates?

If clean, note it. If any flag, route to managing attorney before the transfer becomes effective.

### Step 4: Identify what the incoming attorney needs to know first

This is the human paragraph at the top of the memo. Not a checklist — a narrative the incoming attorney can read in 60 seconds to know what they're walking into. What's the client's story? What's the legal posture? What did the prior attorney commit to? What's the next decision the case needs?

Write this in plain language. Strip the jargon. The new attorney reads this between calls.

### Step 5: Flag the open questions

Things the prior staffer knew but didn't write down — or things that are unclear from the file. These need explicit surfacing because they're the highest-risk items in any transfer.

Common open questions:
- "Client mentioned a verbal agreement with landlord — terms unclear from notes"
- "Prior attorney told client they would file a motion to vacate by [date] — not sure if filed"
- "Settlement offer made by OC two weeks ago — no record of response"
- "Co-counsel arrangement with [private bar firm] — terms in their head, not in the file"

Each open question gets a `[VERIFY: ...]` tag and a suggested verification path (call the departing staffer, call opposing counsel, ask the client, check court docket).

### Step 6: Output the transfer memo

## Output

```markdown
# Case Transfer Memo

---
[AI-ASSISTED DRAFT — requires staff analysis and managing-attorney review]

**Privilege and confidentiality.** This memo references client confidences. Keep it in the office's privileged file store. Distribution beyond the office can waive privilege.
---

**Case ID:** [case-id]
**Client:** [name or ID]
**From:** [staff] | **To:** [staff or "unassigned"] | **Effective:** [date]
**Reason:** [departure / leave / role change / intake-to-staff / capacity reassignment / other]
**Funding source(s):** [LSC / IOLTA / specific grant]
**Practice area:** [primary + cross-area]

## What you need to know first

[60-second narrative — the human paragraph]

## Current posture

- **Case stage:** [pre-filing / litigation / post-judgment / administrative / appeal]
- **Filed?:** [yes — court/case# | no]
- **Next court or agency date:** [date] — [purpose]
- **Other live deadlines:** [list, with source per deadline ledger]

## Open client commitments

- [Each thing the prior staffer promised the client, with date promised]
- [Outstanding callbacks owed]
- [Documents the office said it would produce]

## Open internal work

- [Drafts in progress — location, status, what's left]
- [Research outstanding — what was started, what's still needed]
- [Pending managing-attorney review items, if any]

## Open questions — VERIFY BEFORE ACTING

- [Each ambiguity with a `[VERIFY: ...]` tag and a suggested verification path]

## Eligibility flags still live

- [Any FUNDING RESTRICTION flag from intake/screening that has not been cleared]
- [Any income/asset re-verification due]
- [Any conflict flag pending resolution]

## Conflict re-check for incoming attorney

- [Individual conflict check result — clear / flagged]
- [Date run, against which database]

## Document and CMS pointers

- Case file location: [path / CMS link]
- Communication log: [path]
- Deadline entries: [list of IDs in `deadlines.yaml`]
- Prior memos and drafts: [list with paths]

## Premises this memo rests on

- [Sources used to build the memo — case file / comm log / departing staffer interview / etc.]
- [Date of last entry in each source]

## Managing-attorney review notes

- [Whether the transfer queues for sign-off per supervision style]
- [Any item the managing attorney should weigh in on before the transfer is effective]
```

## Save and propagate

- Save the memo at `case-transfers/[YYYY-MM]/[case-id].md`
- Append a one-line entry to `case-transfers/[YYYY-MM]/_summary.md`: "[date] | [case-id] | [from] → [to] | [reason] | [status: pending review / effective / superseded]"
- If a `deadlines.yaml` entry has `owner_staff: [from]`, the deadlines skill will surface the transfer the next time the rollup runs; update the owner field as part of the transfer
- Update CMS assignment if a CMS connector is configured

## What this skill explicitly does not do

- Close the case.
- Reassign deadlines automatically — that's the managing attorney's call after review.
- Make the conflict-check determination — surfaces the result, does not waive or proceed despite a flag.
- Replace the conversation between the outgoing and incoming staffer; supplements it.
- Run when the case is ending; that flow is `/status internal` + CMS close, not this skill.
