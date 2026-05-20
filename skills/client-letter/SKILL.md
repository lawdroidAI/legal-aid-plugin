---
name: client-letter
description: >
  Routine client correspondence from templates — appointment confirmations,
  document requests, brief "we filed it" updates, engagement letters,
  eligibility-decline letters (with referral), case-closing letters. Plain
  language (6th-grade target), language-served-aware (per CLAUDE.md),
  required elements, supervision routing. NOT substantive advice; use
  /status client for that, or a conversation. Use when a staffer needs to
  send routine correspondence to a prospective client, current client, or
  declined-matter caller.
argument-hint: "[type — appointment | doc-request | update | engagement | decline | closing] [case-id] [optional: language]"
---

# /client-letter

1. Load `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → plain-language standards, supervision style, office contact info, languages served, funder considerations.
2. Load the matter's case file → intake, eligibility-screening output, accepted/declined status, recent comms log.
3. Match the letter type to template. Apply the office's standard format (uploaded at cold-start or default).
4. Plain-language check: 6th-grade reading level, short sentences, active voice, no legalese.
5. Apply language: English default, or one of the office's served languages if specified — with translation-review flag.
6. Output with AI-assisted label, supervision routing per office model.

Scope: routine only. Substantive advice → `/status client` or a conversation. Bad news that's more than a single fact → conversation, not letter.

```
/legal-aid:client-letter appointment case=marisol-2026-04
/legal-aid:client-letter doc-request case=marisol-2026-04 lang=es
/legal-aid:client-letter decline case=screen-2026-447
/legal-aid:client-letter closing case=jackson-2025-12
```

---

# Client Letter: Routine Correspondence

## Purpose

Civil legal aid offices send a steady stream of routine correspondence: "your appointment is Tuesday at 2pm," "please bring your lease," "we filed your answer," "we're closing your case," "we couldn't take your matter, but here's a referral." Each one is the same letter the office wrote last week, and the week before. The staffer's time should not go to typing them.

This skill produces routine letters from templates. The point is speed for the staff, and plain language for the client — at a reading level appropriate to civil legal aid client populations, in a language the client actually speaks if the office serves it.

**Scope: routine.** Substantive legal advice does not go in a templated letter. Bad news — the case isn't going well, the office is withdrawing, the appellate court ruled against — does not go in a templated letter. Those go in a conversation, sometimes followed by a `/status client` update. This skill handles the routine traffic, not the consequential communications.

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → office contact info, plain-language standard, languages served, supervision style, funder considerations (some funders require specific language in declines or engagement letters).

The matter's case file → client name, address, language preference, accepted/declined status, current matter posture, recent comms.

If the office uploaded sample letters at cold-start, the skill matches their format — letterhead structure, sign-off conventions, signature block. If not, generic format with a flag noting it.

## Letter types

### appointment

**Confirms an upcoming appointment with the client.**

Required elements:
- Date, day of week, time, duration
- Location (in-person address with directions / call-in info for phone / video link for video)
- Who the client will meet with (staffer name and role)
- What to bring
- How to reschedule if needed (phone number, deadline for advance notice)
- Office contact for questions

Plain-language conventions:
- "Tuesday, May 22 at 2:00 PM" — not "May 22nd, 14:00"
- "Please bring" — not "kindly provide"
- "Call us at [number] if you can't make it" — not "in the event of inability to attend"

### doc-request

**Requests specific documents from the client.**

Required elements:
- Why the documents are needed (general — "for your case" — not specific legal theory)
- Specific list of documents
- How to provide (drop off, mail, fax, email upload, in-person at appointment)
- Deadline (concrete date)
- What to do if the client doesn't have a document or can't get it (call us, don't ignore the letter)
- Office contact

For each requested document, plain language description:
- "Your lease — the paper you signed when you moved in" (not "your tenancy agreement")
- "Pay stubs for the last three months" (not "wage documentation for the immediately preceding three-month period")
- "The court papers you received" (not "the summons and complaint")

### update

**Brief "we did the thing" status note.**

Scope: a single concrete fact the client should know. Examples:
- "We filed the answer to the eviction in court yesterday."
- "Your benefits hearing is set for [date]. We'll send a separate letter with details."
- "The judge granted the protective order. The order is good for [period]."

Anything more substantive — strategy, what to expect at a hearing, why something turned out a certain way — goes in `/status client` or a conversation.

Required elements:
- The single fact
- What it means in plain language (one or two sentences)
- What happens next (concrete and short)
- What the client should do (often "nothing, we'll be in touch")
- Office contact

### engagement

**Engagement / retainer letter for newly-accepted matter.**

Required elements (these are office- and jurisdiction-specific; consult CLAUDE.md and any practice-area guide):
- Identification of the parties (client name, the office representing)
- Scope of representation — what the office is doing, what it is not doing
- Funder allocation — under which funding source the matter is being handled (this affects the limitations on representation)
- Funder-restriction notice if applicable — what the office cannot do under the funder's rules (e.g., LSC § 1626 limitations on certain representation, class-action prohibition, etc.)
- Confidentiality — what the office will keep confidential and any exceptions
- Communications — how and when the office will reach the client; how the client reaches the office
- No fee for client (legal aid context) — confirm the office charges nothing
- Termination — how either party can end the representation
- Client's responsibilities — keep the office informed of address changes, attend meetings, respond to communications

Practice-area-specific elements:
- DV / VAWA matters: confidentiality of address and contact info, safety planning references
- Immigration: status-specific limitations, third-party communication restrictions
- Public benefits: agency-specific representation authority forms (G-28, SSA-1696 equivalents)

**Supervision flag:** engagement letters always require managing-attorney review regardless of supervision mode. The office is committing to representation; the managing attorney signs off.

### decline

**Letter to a caller whose matter was screened or declined.**

Required elements:
- Date of contact
- General reason for decline (without legal advice on the matter itself, which could create the very obligation the office is declining to take on)
- Statement of what the office cannot do — explicit. "We are not able to take your case."
- Critical: a clear statement that the office is NOT representing the caller, NOT giving legal advice on the matter, and the caller should consult another attorney if they wish to pursue the matter
- Statute of limitations / deadline awareness — encourage the caller to act quickly if there's a known deadline
- Referrals — concrete next steps: lawyer referral service phone numbers, court self-help center, private bar pro bono panel, other legal aid orgs with different funding or eligibility
- Rule 1.18 protection — the office still treats what the caller shared in screening as confidential
- Office contact for follow-up questions (with explicit limitation that the office can't help with the matter)

**The decline letter is the highest-malpractice-exposure routine letter in legal aid practice.** Words matter. The skill defaults conservatively — clearly declines, doesn't accidentally create representation, doesn't accidentally give advice. Always queues for managing-attorney review regardless of supervision mode.

### closing

**Letter to a current client whose matter is being closed.**

Required elements:
- Identification of the matter being closed
- Reason for closing — completed (favorable outcome), completed (unfavorable outcome), client request, scope-of-representation completed, time-limited project completed, etc.
- The outcome in plain language (one or two sentences; substantive analysis goes in `/status client` or a conversation)
- What documents the client is being given / where to pick them up / how the file will be retained or destroyed and on what timeline
- What the client should do if the same issue arises again (call back; the matter may be reopenable)
- What the client should do if a different issue arises (call intake; eligibility re-screens)
- Final office contact
- Office's standard retention notice / sign-off language from CLAUDE.md if uploaded

**Supervision flag:** closing letters always require managing-attorney review. The matter is ending; the client may need to be told something the office doesn't want to put in a letter.

## Plain-language conventions (all letter types)

- Reading level: 6th-grade target. Use Flesch-Kincaid as a rough metric if available; otherwise apply the conventions below.
- Sentence length: average 15 words, never over 25.
- Active voice always. "We filed your answer" not "your answer was filed."
- Concrete words. "The papers you got" not "the documents you received."
- Dates in long form. "Tuesday, May 22, 2026" not "5/22/2026."
- Phone numbers in standard local format.
- No legalese. The plug list:
  - "case" not "matter" (most contexts)
  - "the other side" not "the opposing party / respondent"
  - "what the judge decides" not "the court's determination"
  - "papers" not "pleadings / documents / filings"
  - "your lease" not "your residential lease agreement"
  - "money you owe / they owe" not "indebtedness / obligation"
  - "your benefits" not "your public assistance / entitlement"
- No acronyms without first-use expansion.
- Use the client's name and direct address. "Maria, your appointment is..." reads warmer than "Dear Ms. Jones, this is to inform you..."
- The office's culture about formality varies; match what CLAUDE.md / sample letters indicate. Some offices write more formally; some don't. The plain-language goal is constant.

## Language

If the client's primary language is one of the languages the office serves per CLAUDE.md, the skill produces the letter in that language. Always with a flag:

> "Original draft in [language]. AI-generated translations of legal communications require human review by a fluent speaker. Route to [office's translation review process] before sending."

If the language isn't served, the skill produces English and notes: "Client's primary language is [language], not served by this office. Route through your standard translation workflow before sending."

## Supervision routing

- **appointment, doc-request, update:** under "configurable flags" mode, these usually don't flag; under "formal queue" mode, they queue but as a low-priority batch the managing attorney clears quickly.
- **engagement, decline, closing:** always flag and always queue, regardless of supervision mode. These create or end representation; the managing attorney reviews every one.

## Output

```markdown
# Letter: [type] for [Case ID or screen ID]

---
[AI-ASSISTED DRAFT — requires staff review and supervision routing before sending. Strip this label before printing/sending.]

**Type:** [type] | **Audience:** [client / prospective client / former client]
**Language:** [language; translation review flag if non-English]
**Funder considerations:** [any flags propagated; relevant in engagement/decline letters most]
**Supervision routing:** [per office model and letter type]
---

[Letter body per template]

[Sign-off and signature block]

---

## Pre-sending checklist
- [ ] Reading level check: 6th-grade target met
- [ ] Plain-language conventions applied (active voice, concrete nouns, no legalese)
- [ ] Required elements for letter type present
- [ ] Office contact info current (phone, address, hours)
- [ ] Client's name and address verified against current case file
- [ ] If translated: human review by fluent speaker completed
- [ ] If engagement/decline/closing: managing-attorney review completed
- [ ] If funder-restriction language required: included accurately

## Premises this letter rests on
- Letter type: [type]
- Matter status: [from case file]
- Plain-language standard: 6th-grade target (CLAUDE.md plain-language config)
- Language: [language]
- Template basis: [office sample letter at <path> / generic template]
- Office contact info: [from CLAUDE.md; verify current]

## What to verify before sending
- Office contact info matches current (phone, address, hours)
- Client address is current per case file (clients in housing matters move frequently)
- Date, time, location of any appointment / hearing match what's in the matter's deadline ledger
- For decline letters specifically: no accidental advice, no language that implies representation, referral information is current
```

## Cross-skill propagation

- Sent letters are logged to the comms log via `/client-comms-log` after sending.
- Engagement letters generated here propagate the funder allocation and supervision model into the matter's active configuration; the `/draft` and `/memo` skills read it.
- Decline letters generated for screened-out callers feed into the office's intake statistics (which `/lsc-report` uses for aggregate reporting on declined matters where the funder requires it).
- Closing letters logged here become the trigger for the matter's archival workflow.

## What this skill does NOT do

- Substitute for substantive client communication. Strategy, bad news, complex updates → conversation or `/status client`, never this skill.
- Decide whether to send. Staff and managing attorney decide; the skill produces the document.
- Override translation review. AI translation is a first draft; fluent human review is required for civil legal aid client communication.
- Provide legal advice in a decline letter. The decline letter declines representation; it does not advise the caller on what to do with their matter (other than referrals).
- Substitute for engagement / decline / closing managing-attorney review. These are consequential communications regardless of supervision mode.
- Use the client's first name without confirming. Some clients prefer formal address; the case file or comms log should indicate preference. Default to the more formal address if unclear.
