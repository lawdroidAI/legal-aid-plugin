---
name: client-intake
description: >
  Structured intake on a matter that has cleared eligibility screening.
  Pulls upstream conversation context if LIA-MCP or VIA-MCP is connected,
  routes to practice-area template, spots cross-area issues, runs full
  conflict check, propagates funder-source matrix and funding-restriction
  flags from screening, classifies urgency, and produces a formatted case
  summary the staff attorney analyzes and the managing attorney reviews.
  Does NOT decide case acceptance. Use after /eligibility-screening, when
  starting a new client intake, or writing up a new client's situation.
argument-hint: "[case-id from screening] [optional: practice area hint]"
---

# /client-intake

1. Load `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → practice areas, intake templates, funder restrictions, supervision style, conflict-check process, flag triggers.
2. Check for an eligibility-screening result. If none, route to `/eligibility-screening` first — intake does not run cold.
3. If LIA-MCP or VIA-MCP is connected, pull the upstream conversation for the case. Pre-load what the caller already told us.
4. Use the workflow below: route to practice-area template, listen for cross-area issues, run the full conflict check, classify urgency.
5. Output a formatted case summary with AI-assisted label, propagated screening flags, and supervision routing.

```
/legal-aid:client-intake case=jones-2026-04
```

---

# Client Intake

## Purpose

Intake is the largest single time sink in civil legal aid practice. A staff attorney spends 30-60 minutes interviewing, another 30-60 minutes writing it up, more time spotting issues and tagging conflicts. Meanwhile the waitlist grows.

This skill structures the intake conversation on a matter that has *already cleared eligibility screening*, propagates everything the eligibility screen captured, pulls any upstream LIA or VIA conversation if those connectors are wired, produces the case write-up, spots issues across practice areas, runs the full conflict check (the screening did a preliminary), classifies urgency, and produces a summary the staff attorney analyzes and the managing attorney reviews.

**What it doesn't do:** decide whether to take the case. Acceptance is the managing attorney's call. The skill structures and accelerates information-gathering and write-up, not the lawyering.

## Sequencing

This skill runs *after* `/eligibility-screening`. If a staffer tries to start with `/client-intake` cold, the first action is to redirect:

> Intake runs after eligibility screening. Run `/legal-aid:eligibility-screening` first — it captures income, residency, citizenship status, conflict pre-check, and case-type priority that this intake builds on. Without it, we're either duplicating work or skipping screening that legal aid practice requires.

The only exception is if the office's CLAUDE.md indicates a hybrid model where intake and screening run as one combined flow (some court self-help centers do this). In that case, run both inline and produce a combined output.

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → practice areas, intake templates (per practice area if multiple), funder restrictions acknowledged, supervision style, jurisdiction, flag triggers, conflict-check process.

The eligibility-screening result for this matter → funder-source matrix, eligibility-flags-still-live, preliminary-conflict-status, priority classification, suggested-next-step. The intake propagates all of this; nothing gets re-asked or re-decided that screening already settled.

## Read the practice-area guide

Check for a practice-area guide at `~/.claude/plugins/config/claude-for-legal/legal-aid/guides/<practice-area>.md`. If one exists, use its intake questions, red flags, and good-fit criteria instead of the generic defaults below. If one doesn't exist, use the generic intake and note at the end of the summary: "This was a generic intake — the managing attorney can tailor the questions for this practice area with `/legal-aid:build-guide`."

Re-check for the guide after Step 1 routing — the guide path depends on which practice area the intake landed in.

## Pull upstream conversation if available

If the office has the LawDroid LIA-MCP or VIA-MCP connectors enabled (check the integrations table in CLAUDE.md), and the eligibility screening captured a `lia_conversation_id` or `via_call_id` for this caller, pull the full conversation:

- `lia_get_conversation(id)` or `via_get_call(id)` → narrative the caller already gave
- `lia_get_triage(id)` or `via_get_triage(id)` → practice-area classification, urgency, language, jurisdictional signals already captured
- `lia_get_eligibility_leads(id)` → income, citizenship, residency, conflict signals the upstream conversation surfaced

If pulled, label clearly in the output: "Upstream conversation pre-loaded from LIA on [date] / VIA call on [date]. Caller narrative below is from that source. Verify on intake — situations evolve between first contact and case acceptance."

Do *not* treat upstream data as already-verified. The caller's situation may have shifted (eviction date now imminent vs. earlier abstract concern; income source changed; second incident occurred). Intake re-checks every load-bearing fact.

## Workflow

### Step 1: Practice area routing

If practice area is unambiguous from screening or upstream conversation, skip to Step 2. Otherwise, route:

> "Thanks for coming in. Eligibility cleared, so let's get the details for the case. Tell me what's going on — in your own words, what brought you to the office?"

From the answer, identify primary practice area and any cross-area issues. If multiple practice areas show up, note them all — the office may handle one and refer the other, or handle both with cross-area awareness.

### Step 2: Practice-area-specific intake

Use the template from CLAUDE.md for this practice area. Generic defaults if none provided:

**Housing:**
- Tenancy details (lease type, length, rent amount, deposit, who's on the lease)
- Nature of issue (notice received? lockout threat? habitability? deposit dispute? lease termination?)
- Documents (notice, lease, repair requests, payment records)
- Timeline (when did this start? deadlines on notice?)
- Habitability conditions
- Subsidy involvement (Section 8, public housing, LIHTC)

**Family:**
- Relationship structure (current and historical)
- Children (custody / support / visitation status)
- Safety concerns (DV history, current threat, protective orders)
- Existing orders (custody, support, restraining)
- Financial picture
- Goals (legal and practical)

**Consumer:**
- Debt or transaction at issue (creditor, amount, age)
- Documents (statements, agreements, demand letters, summons)
- Communications received (frequency, content, harassment indicators)
- Procedural posture (pre-litigation, served, default judgment, post-judgment, garnishment)
- Defenses available (statute of limitations, identity, dispute)

**Immigration:**
- Status (current, prior, family members')
- History (entry, prior applications, removal proceedings)
- Documents (passport, prior filings, court notices)
- Goals (asylum, adjustment, naturalization, defense)
- Vulnerabilities (DV under VAWA, U/T visa basis, age-out concerns)

**Public benefits:**
- Benefit at issue (SSI, SSDI, SNAP, Medicaid, TANF, unemployment, housing assistance)
- Status (pending, denied, terminated, reduced, sanctioned)
- Notice received (date, basis, appeal deadline)
- Documents (notices, application materials, work history if disability)
- Underlying medical / household / employment facts as relevant

**Civil rights:**
- Incident or pattern (when, where, who, what)
- Documents (correspondence, employment records, agency filings)
- Prior agency proceedings (EEOC charge, state human rights, ADA complaints)
- Statute of limitations awareness

**Elder / DV / other:** use practice-area template if specified; otherwise narrative intake with explicit safety check.

### Step 3: Cross-area issue spotting

Through the intake, listen for issues outside the primary practice area:

- Housing client mentions DV → flag for protective order referral or in-office DV unit
- Family client mentions immigration status → flag § 1626 implications and possible VAWA pathway
- Consumer client mentions bankruptcy → flag interaction with the matter
- Any client mentions criminal-adjacent matters → flag for separate referral; civil legal aid does not cover

Each cross-area issue gets recorded in the output with a routing recommendation (refer / supplement / flag-only).

### Step 4: Full conflict check

The eligibility screen did a preliminary check. Intake runs the full one per the office's process documented in CLAUDE.md:

- Opposing party against the office's full case database
- Related parties (other tenants in the building, other family members in the matter, co-debtors, related agencies)
- Positional conflicts (are we taking a position that would hurt another current client?)
- Prior office contact with this prospective client (Rule 1.18-aware — prior contact may have created limited duties even if the office didn't take that matter)

If the office's CMS is connected via MCP, run the check there. If not, output the structured query the intake specialist should run manually.

Flag every hit for managing-attorney review before acceptance. Do not resolve conflicts — surface them.

### Step 5: Urgency classification

Classify the matter on urgency:

| Tier | Means | Examples |
|---|---|---|
| **Emergency** | Irreversible harm within days | Lockout tomorrow, hearing this week, deportation imminent, safety threat |
| **Time-sensitive** | Deadline within 2-4 weeks | Court date in a month, appeal deadline next week, benefits termination effective in 14 days |
| **Standard** | No imminent deadline | General advice matter, ongoing benefits issue, document preparation |

Emergency matters route immediately to managing-attorney attention. The intake output flags this prominently and does not wait for standard supervision queue cycles.

### Step 6: Output the intake summary

## Output

```markdown
# Client Intake: [Case ID]

---
[AI-ASSISTED DRAFT — requires staff analysis and managing-attorney review]

**Privilege and confidentiality.** This intake contains information disclosed by a prospective client. Rule 1.18 duties of confidentiality apply regardless of whether the office accepts the matter. Keep in the office's privileged file store; distribution decisions go through the managing attorney.
---

**Date:** [date] | **Intake by:** [staff] | **Practice area:** [primary + any cross-area]
**Urgency:** [Emergency / Time-sensitive / Standard]
**Eligibility status:** Cleared on [date] under [funding source(s) from screening matrix]
**Upstream source (if any):** LIA conversation [id] / VIA call [id] / fresh intake

## Caller's situation

[Narrative in caller's words. Pulled from LIA/VIA if available; supplemented or replaced based on intake interview. Mark sections that were re-verified in this intake vs. carried from upstream.]

## Practice-area details

[Practice-area-specific section per Step 2 template. Each fact tagged with source: client-stated, documented, observed, or upstream-only-not-verified.]

## Cross-practice-area flags

- [Each non-primary issue surfaced, with routing recommendation: refer / supplement / flag-only]

## Conflict check (full)

- Opposing party: [name(s)] — [CMS clear / CMS hit — see entry [ID] / manual check needed]
- Related parties: [list with results]
- Positional: [any concerns vs current office cases]
- Prior office contact: [none / yes — [date], matter type, outcome — Rule 1.18 implications noted]

## Funder-source matrix (propagated from screening)

| Funding source | Status from screening | Notes for intake |
|---|---|---|
| [LSC] | [Eligible/Ineligible] | [Any § 1626 or other restriction flags still live] |
| [IOLTA / other] | [Eligible/Ineligible] | [Any carve-out or alternative pathway] |

## Active flags (propagated and added)

- [Each `[FUNDING RESTRICTION: ...]` flag from screening — re-stated]
- [Any new `[FUNDING RESTRICTION: ...]` flag this intake surfaces]
- [Any conflict flag from Step 4]
- [Any urgency flag from Step 5]
- [Any cross-area referral flag from Step 3]

## Suggested next step

- [Specific next action based on urgency and managing-attorney supervision style — e.g., "Route to managing attorney for acceptance decision by end of day (Emergency)"; "Queue for next case rounds Tuesday (Standard)"; "Refer cross-area family issue to in-office DV unit; retain housing matter pending acceptance"]

## Premises this intake rests on

- [Sources used: client interview / LIA / VIA / documents provided / case-management system / staff recollection]
- [Date of each source]
- [Anything stated by the client but not verified — list explicitly]

## Verification prompts before relying on this intake

- Verify documented facts against the documents the client brought or sent (lease, notice, court papers, benefits letter)
- Re-confirm any fact carried from upstream LIA/VIA if more than 7 days old or if the situation has changed
- Run the CMS conflict check if not already automated
- Confirm current funder-restriction status against the office's compliance documentation
- For Emergency matters: confirm next step is in motion (managing attorney notified, court calendar checked, etc.) before close of business today
```

## Cross-skill propagation

- The funder-source matrix and `[FUNDING RESTRICTION: ...]` flags propagate from intake into `/draft`, `/memo`, `/status`, `/client-letter`, and `/lsc-report` for the matter.
- Cross-area issues marked "refer" become candidates for `/client-letter referral`.
- Cross-area issues marked "supplement" become candidates for a second intake under the supplementary practice area, sharing the same case ID.
- Conflict flags from this skill require managing-attorney resolution before any drafting begins. The `/draft` skill checks for unresolved conflict flags and refuses to proceed.
- Urgency: Emergency intakes set a flag the `/deadlines` skill picks up and surfaces in every daily rollup until cleared.
- The output is saved to the office's case file location per CLAUDE.md.

## What this skill explicitly does not do

- Decide whether to take the case. Acceptance is the managing attorney's call after this intake; the skill provides the input.
- Run cold. Eligibility screening is the prerequisite (with the documented hybrid exception).
- Verify documents. The intake captures what the caller said and what the caller showed; verification against the actual document remains the staffer's job.
- Resolve conflicts. Surfaces them; the managing attorney resolves.
- Replace Rule 1.18 disclosure to the caller. Rule 1.18 obligations and the office's intake disclosures (whether the office takes the matter, what happens to information if the office declines, who has access) are spoken by the staffer, not generated by the tool.
- Override emergency routing. If the urgency classification is Emergency, the skill flags prominently and the staffer surfaces it to the managing attorney immediately, regardless of the supervision-style configuration.
