---
name: status
description: >
  Case status summary by audience — client-facing (6th-grade plain
  language), internal (for the managing attorney), court-ready (formal
  caption format per local rules), or funder (ad-hoc update for a funder's
  monitoring request, separate from the full /lsc-report cycle). Same facts,
  different framing and depth. Use when a staffer needs to update the
  client, brief the managing attorney, prepare a court status report, or
  respond to a funder's request for an update on a specific matter.
argument-hint: "[client | internal | court | funder] [case-id] [optional: language code for client]"
---

# /status

1. Load `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → supervision style, plain-language standards, jurisdiction, funders, languages served.
2. Load the matter's case file → intake, eligibility-screening output, prior memos, drafts, comms log, deadlines, funder allocation.
3. Read the practice-area guide at `guides/<practice-area>.md` if one exists.
4. Generate for the specified audience:
   - `client` — 6th-grade plain language; what happened, what's next, what they do, how to reach the office; in client's primary language if specified
   - `internal` — procedural posture, work done since last check-in, upcoming deadlines, decisions the managing attorney needs to make, staff's own assessment
   - `court` — formal status report in caption format per local rules
   - `funder` — ad-hoc update for funder's monitoring, formatted to match the office's reporting conventions for that funder
5. Apply supervision routing per audience: client-facing and court-ready usually flag for managing-attorney review; funder usually flags for grants-person + managing-attorney; internal usually doesn't queue.

```
/legal-aid:status client case=marisol-2026-04
/legal-aid:status client case=marisol-2026-04 lang=es
/legal-aid:status internal case=marisol-2026-04
/legal-aid:status court case=marisol-2026-04
/legal-aid:status funder case=marisol-2026-04
```

---

# Status: Audience-Aware Case Summaries

## Purpose

Civil legal aid offices generate enormous volumes of status updates — to clients (often the most under-resourced communication, despite being the most important), to managing attorneys, to courts, and increasingly to funders requesting ad-hoc visibility into specific matters. Same facts, completely different documents. This skill takes the case file and produces the right summary for the right reader.

The four audiences are non-interchangeable. A client-facing update written for the managing attorney confuses the client. An internal update written for the client misses the procedural detail the managing attorney needs. A court-ready status report needs caption format and the formality of a filing. A funder update reads against the funder's monitoring conventions, not the office's internal vocabulary.

**What it doesn't do:** decide what to communicate. The staffer and managing attorney decide what the client needs to know, what the managing attorney needs to weigh in on, what the court needs informed of, what the funder is asking about. The skill produces the document; the human decides content.

## Audience selection gates

If the audience isn't specified, the skill asks:

> Who's this for? (client / internal / court / funder.) Same facts, different document — pick one. If you need updates for two audiences, run the skill twice.

For client audience, additionally:

> What language? Default is English. The office serves [languages from CLAUDE.md]. If the client's primary language is one of those, I can produce the update in that language; if not, I'll produce English and you'll route to translation.

For funder audience, additionally:

> Which funder? The matter is allocated to [funder(s) from intake]. Funder updates are formatted to match each funder's monitoring conventions — those may differ.

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → office profile, plain-language standards, jurisdictions, supervision style, languages served, funder profiles, contact info.

The matter's case file → intake summary, eligibility-screening output, prior memos and drafts, comms log, deadline entries, all `[FUNDING RESTRICTION: ...]` flags, prior status reports.

`~/.claude/plugins/config/claude-for-legal/legal-aid/guides/<practice-area>.md` if one exists.

## Audience-specific generation

### Client audience

**Reading level: 6th grade.** This is non-negotiable. Civil legal aid client populations include people with limited formal education, people with limited English proficiency, people experiencing cognitive impacts of trauma, stress, or illness. A client letter at lawyer-reading-level fails the client even when every word is technically accurate.

**Structure:**

1. **What happened** — one to three short sentences. What occurred since the last update.
2. **What's next** — what the office will do, by when. Concrete dates if known; "in the next [period]" if not.
3. **What the client does** — specific actions the client needs to take, with deadlines.
4. **How to reach the office** — phone number, address, hours, what to do if it's an emergency.

**Language conventions:**

- Short sentences. Average 15 words; never over 25.
- Active voice. "The judge will hear your case on May 22" — not "your case will be heard."
- Concrete nouns. "The eviction papers" — not "the pleadings."
- No legalese. "Your case" — not "your matter." "The other side" — not "the respondent." "The judge will decide" — not "the court will adjudicate."
- No acronyms without full-form expansion. "Department of Social Services (DSS)" on first use, "DSS" after.
- Date format: "Tuesday, May 22, 2026" not "5/22/26."
- Phone format: full local number with area code.

**Translation:** If a non-English language is requested and the office's CLAUDE.md indicates the language is served, produce the update in that language. Tag at the top: `Original draft in [language]. Confirm with bilingual staff or translator before sending — AI-translated text requires human review for civil legal aid communication.`

**Supervision flag:** Client-facing communications usually flag for managing-attorney review under "configurable flags" mode and always queue under "formal queue" mode. The skill labels accordingly.

### Internal audience

**For the managing attorney.** Procedural-posture-aware, decision-focused.

**Structure:**

1. **Procedural posture** — where the matter is in its lifecycle right now (pre-litigation / litigation / pending hearing / post-decision / appeal / administrative review). One sentence.
2. **Work done since last check-in** — what the staffer has done. Concrete: "filed the answer on [date]," "drafted the memo on habitability defense (attached)," "called the client three times; reached on [date] and confirmed she's still living in the unit."
3. **Upcoming** — deadlines, hearings, client meetings, document due-dates. Listed with dates.
4. **Decisions the managing attorney needs to make** — explicit. "Should I send the proposed settlement to OC, or hold pending [event]?" "The funder-restriction flag I raised in the memo is still live — can you confirm we're proceeding under [funder]?"
5. **Staff's own assessment** — the staffer's read. "I think we have a strong habitability defense based on the documented complaints; I'm less confident about the retaliation theory until I research more."

**Length:** terse. The managing attorney is reading 10 of these in a row. Aim for a screen.

**Supervision flag:** internal usually doesn't queue — it goes straight to the managing attorney's inbox or whatever the office uses for case rounds.

### Court audience

**Formal status report in caption format.** This is a filing in everything but name; treat it as a draft document.

**Structure:**

1. **Caption** — court, division, case caption, case number, document title ("Status Report" or whatever the local court uses)
2. **Introductory paragraph** — counsel of record, on behalf of [party], submits this status report pursuant to [court's request / standing order / general practice]
3. **Procedural history** — short. What's happened in the case to date, with dates and document references
4. **Current status** — what's happening now
5. **Anticipated next steps** — what counsel expects to occur, with timing
6. **Any matters for the court's attention** — open motions, scheduling needs, requests
7. **Signature block** — counsel's name, bar admission, office contact, signature line, date

Apply jurisdiction-specific formatting from CLAUDE.md local rules ingest. Tag formatting as `[VERIFY against current local rules of [court]]` regardless.

**Supervision flag:** court-bound documents always queue under formal review or always flag under configurable flags. Court-bound, regardless of supervision mode, get reviewed by the managing attorney before filing.

### Funder audience

**Ad-hoc update for a funder's monitoring request.** Distinct from `/lsc-report`, which is the full reporting cycle. This is "the funder emailed and asked about Maria Jones's case" or "the funder is conducting a monitoring visit and wants a sample of active matter updates."

**Structure:**

1. **Header** — funder name, monitoring reference if any, date, contact
2. **Case overview** — practice area, funding allocation, intake date, current status. Two to four sentences.
3. **Services provided** — what work the office has done on this matter. Funder-format-specific (LSC-style problem-code/case-type tagging if LSC; narrative if foundation; outcome-focused if HUD).
4. **Outcome to date** — what's been achieved. If matter is open, say so; if closed, the outcome.
5. **Confidentiality notes** — any client information that's not for funder distribution (especially relevant for VAWA-funded matters; some details may need aggregation rather than disclosure)
6. **Cost / hours allocation** — if the funder asks for it and the office's case management captures it; otherwise note that hours are reported via the regular reporting cycle

**Supervision flag:** funder updates always route to the office's reporting owner (grants person if there is one, managing attorney otherwise) for review before sending. Funder communications can affect compliance posture; nothing goes out without that review.

**Special handling for VAWA funder updates:** confidentiality obligations under 42 USC § 13925(b)(2) restrict what can be shared. The skill aggregates or redacts as needed and surfaces what's been protected:

> "The funder asked about three specific matters. I've produced updates for [two] in full; for the third, the demographic and geographic detail risks identifying the client given the small population in [county]. I've aggregated that update. Review before sending."

## Output

Per audience. Templates differ. All outputs carry:

```markdown
---
[AI-ASSISTED DRAFT — requires staff review and per-office supervision routing before sending]
---

[Audience-specific output per above]

## Supervision routing
[Per office model and audience]

## Premises this status rests on
- Matter context: [intake summary date, prior memos, recent comms log]
- Funder allocation: [funder(s)]
- Reading level (if client): [target — 6th grade default]
- Language (if client): [language; AI translation requires human review]
- Local rules (if court): [path or "verify against current local rules"]
- Funder monitoring conventions (if funder): [path or "match prior funder report format"]

## Verification before sending
[Audience-specific verification checklist]
```

## Cross-skill propagation

- Client-audience status outputs are logged to the comms log via `/client-comms-log` after sending.
- Funder-audience updates feed into `/lsc-report` for the next reporting cycle — the data extracted here may be used aggregated in the full report.
- Court-audience updates that become filings join the matter's case file alongside drafts.

## What this skill does NOT do

- Decide what to tell the audience. Staff and managing attorney decide; skill produces the document.
- Substitute for translation review. AI translation needs bilingual human review for civil legal aid client communication; the skill flags this explicitly.
- Override funder-confidentiality requirements. VAWA in particular constrains what can be disclosed; the skill aggregates or refuses where required.
- Replace court-filing review. Court-bound output is a draft requiring managing-attorney review, regardless of supervision mode.
- Provide legal advice in client-audience outputs. Plain-language status of the matter, not strategic advice. Strategic advice goes through a conversation, not a templated letter.
