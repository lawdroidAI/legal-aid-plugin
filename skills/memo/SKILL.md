---
name: memo
description: >
  IRAC-scaffolded case analysis memo with research gaps flagged and a
  funder-restriction analysis section when any [FUNDING RESTRICTION] flag
  is live on the matter. Rule blocks are RESEARCH NEEDED; Application is
  STAFF ANALYSIS prompts; Conclusion is blank. The scaffold, not the
  analysis. Use when a staff attorney needs to scaffold a case analysis
  memo, write up their analysis, or build an IRAC memo for a managing-
  attorney review.
argument-hint: "[case-id] [optional: specific issue to focus]"
---

# /memo

1. Load `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → practice areas, jurisdiction, supervision style, funder restrictions.
2. Use the workflow below. Read intake summary, eligibility-screening output, prior drafts, and any prior memo on the matter.
3. Read the practice-area guide at `guides/<practice-area>.md` if one exists.
4. Frame issues as questions. Scaffold IRAC for each — Rule blocks are RESEARCH NEEDED, Application is STAFF ANALYSIS prompts, Conclusion is blank.
5. If any `[FUNDING RESTRICTION: ...]` flag is live on the matter, scaffold a Funder-restriction analysis section.
6. Strengths / weaknesses / open questions. Research gaps summary.
7. Output with prominent "the analysis is yours" label.

```
/legal-aid:memo case=marisol-2026-04
/legal-aid:memo case=marisol-2026-04 issue=habitability
```

---

# Memo: Internal Case Analysis

## Purpose

The case analysis memo is where the staff attorney's thinking lives. It's what the managing attorney reads to decide strategy, what gets handed to a successor on staff transition, what informs the brief or the closing argument or the settlement posture.

This skill provides the IRAC scaffolding and flags every research gap. The Rule blocks are `[RESEARCH NEEDED: ...]` because legal research is the staff attorney's job. The Application blocks are `[STAFF ANALYSIS: ...]` prompts because applying law to facts is the lawyering. The Conclusion is left blank because that's where the staff attorney's judgment lands.

**The analysis is the staff attorney's.** This skill structures; it doesn't conclude.

## Sequencing

The skill expects matter context — an intake summary at minimum, ideally also prior memos and any practice-area guide. Without context, the output is generic IRAC and far less useful. If no case-ID is provided and no matter is in scope, ask:

> No matter context found. Run `/legal-aid:memo case=<case-id>` against a matter that has an intake on file, or paste the relevant intake/case notes inline. Without facts, this skill produces a generic IRAC scaffold that's less useful than the time it takes.

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → office profile, practice areas, jurisdiction, supervision style.

The matter's case file → intake summary, eligibility-screening output, all `[FUNDING RESTRICTION: ...]` flags currently live, any prior drafts and memos, comms log.

`~/.claude/plugins/config/claude-for-legal/legal-aid/guides/<practice-area>.md` if one exists → red flags, escalation posture, funder-restriction overrides, local quirks.

## Workflow

### Step 1: Issue framing

Read the intake summary. Extract the issues — both the staff attorney's named issue (if provided) and any that emerge from the facts. Frame each as a question:

> "Did the landlord's failure to repair the heater for six months constitute a breach of the warranty of habitability under [state] law, sufficient to support a habitability defense to the eviction action?"

Issues come from facts, not assumptions. If the intake mentions a fact the staff attorney didn't flag as an issue but that's plausibly issue-creating, list it and ask:

> "I see [fact] in the intake. Is this also an issue you want the memo to cover? It could be relevant to [possible theory]. If yes, I'll include it. If no, I'll leave it for later."

### Step 2: Per-issue IRAC scaffold

For each issue, build:

**Issue**
> [The question, restated precisely. Includes the controlling jurisdiction, the procedural posture, and what's at stake — not just the abstract legal question.]

**Rule**
> `[RESEARCH NEEDED: <specific rule statement needed>]`
> 
> What to research:
> - The governing statute(s): [specific likely-relevant statute names, with [VERIFY] tags]
> - The controlling case law in [jurisdiction]: [areas of inquiry, with [VERIFY] tags]
> - Any administrative regulation: [if applicable]
> - Any local court rule: [if applicable]
> 
> What the rule should answer:
> - [Specific element 1 — e.g., "What constitutes a 'material' breach of warranty in [state]?"]
> - [Specific element 2 — e.g., "What notice is required before a tenant may assert habitability as a defense?"]
> - [Specific element 3 — e.g., "Are damages required to be proven, or is the breach itself enough for affirmative defense?"]

**Application**
> `[STAFF ANALYSIS: applying the rule to the facts of this matter]`
> 
> The facts at issue from intake:
> - [Fact 1, with source citation to intake summary]
> - [Fact 2, with source citation]
> - [Fact 3, with source citation]
> 
> Questions for the staff attorney's analysis:
> - [Specific question 1 — e.g., "Did the broken heater since November constitute a 'material' breach? The rule will define material; how do these facts measure against it?"]
> - [Specific question 2 — e.g., "Did the tenant provide notice as the rule requires? Intake shows complaints to the landlord in December and January — does that satisfy?"]
> - [Specific question 3 — e.g., "What weight should the absence of a written notice carry?"]

**Conclusion**
> `[STAFF CONCLUSION: ...]`
> 
> *Left blank. The conclusion is the staff attorney's, after research and analysis. The managing attorney will review it.*

### Step 3: Funder-restriction analysis

If any `[FUNDING RESTRICTION: ...]` flag is live on the matter, add a dedicated section:

**Funder-restriction analysis**

For each live flag:

> **Restriction:** [The flag — e.g., "LSC class-action prohibition (45 CFR § 1617)"]
> 
> **Why flagged:** [From intake — e.g., "This matter could support a class action with other tenants at the same property"]
> 
> **Implications for this matter:**
> - `[STAFF ANALYSIS: Does the contemplated action engage the restriction? On what facts?]`
> - `[RESEARCH NEEDED: Current LSC guidance on this restriction's application to this fact pattern]`
> - **Options if restriction applies:**
>   - Pursue under non-LSC funds (IOLTA, state, foundation) if matter is eligible
>   - Limit the action to avoid engaging the restriction (e.g., individual rather than class)
>   - Decline the action and refer
> - **Managing-attorney decision required before any filing.**

The funder-restriction analysis is a required IRAC adjacent — it's not optional. Skills that produce filings (`/draft`) read this analysis and refuse to proceed if the analysis indicates a possible restriction engagement that hasn't been resolved.

### Step 4: Strengths, weaknesses, open questions

After the per-issue IRAC scaffolds, three short sections the staff attorney fills in:

**Strengths**
> `[STAFF ANALYSIS: What argues for the client's position? Why might this matter prevail?]`
> 
> Possible elements to address (not exhaustive):
> - Factual strength: [prompts based on intake facts that look favorable]
> - Legal strength: [prompts based on the research the staff attorney will do]
> - Procedural posture: [prompts based on what stage the matter is at]
> - Equitable factors: [prompts based on the facts that might move a judge]

**Weaknesses**
> `[STAFF ANALYSIS: What argues against the client's position? What's the opposing party going to say?]`
> 
> Possible elements to address:
> - Factual weakness: [prompts based on intake facts that look unfavorable]
> - Legal weakness: [prompts based on the rule the staff attorney will research]
> - Procedural risks: [prompts based on deadlines, missed steps, prior orders]
> - Credibility issues: [prompts based on documentation or witness availability]

**Open questions**
> `[STAFF ANALYSIS: What does the staff attorney not yet know, that matters?]`
> 
> Possible open questions surfaced by the intake:
> - [Open question 1 — e.g., "Did the tenant pay rent during the period of habitability dispute?"]
> - [Open question 2 — e.g., "Is there documentary evidence of complaints made to the landlord?"]
> - [Open question 3 — e.g., "Does the lease have any provision waiving the warranty of habitability? (Many such waivers are unenforceable, but the lease language matters.)"]

### Step 5: Research-gap summary

A final section listing every `[RESEARCH NEEDED: ...]` block from the memo, consolidated:

**Research gaps to close before this memo is complete**

1. [Rule 1 — controlling statute and case law in jurisdiction]
2. [Rule 2 — ...]
3. [Funder-restriction guidance, if applicable]
4. [Local rule or judge preference, if applicable]

The staff attorney works through this list (perhaps using `/research-start [issue]` to scaffold each item) and fills in the Rule blocks above. Then they work through the `[STAFF ANALYSIS: ...]` prompts. Then they write the `[STAFF CONCLUSION: ...]`. Then the memo goes to managing-attorney review.

### Step 6: Output

## Output

```markdown
# Case Analysis Memo: [Case ID]

---
[AI-ASSISTED SCAFFOLD — the analysis is the staff attorney's; this skill provides structure and flags gaps]

**Privilege and confidentiality.** This memo contains work product subject to attorney-client privilege and attorney work-product doctrine. Keep in the office's privileged file store.
---

**Date:** [date] | **Drafted by:** [staff] | **Matter:** [Case ID / client name]
**Practice area:** [primary + cross-area]
**Funder allocation:** [funder(s)]
**Procedural posture:** [pre-litigation / litigation / administrative / appeal]
**Live funder-restriction flags:** [list, or "none"]

## Issues presented

[List of issues from Step 1]

## Issue 1: [Question]

### Rule
[RESEARCH NEEDED block per Step 2]

### Application
[STAFF ANALYSIS block per Step 2]

### Conclusion
[STAFF CONCLUSION — blank]

## Issue 2: [Question]

[etc.]

## Funder-restriction analysis

[Per Step 3 if any flags live; omit section if none]

## Strengths

[Per Step 4]

## Weaknesses

[Per Step 4]

## Open questions

[Per Step 4]

## Research gaps to close

[Per Step 5]

## Premises this scaffold rests on

- Intake summary: [date, staffer]
- Eligibility-screening output: [date, staffer]
- Prior memos on this matter: [list, or "none"]
- Practice-area guide: [version, or "no guide for this practice area; using generic defaults"]
- Funder-restriction flags read from intake: [list]

## Verification prompts before relying on this memo

- Complete every `[RESEARCH NEEDED: ...]` block with verified authorities
- Work through every `[STAFF ANALYSIS: ...]` prompt with the staffer's own reasoning
- Resolve every funder-restriction flag with managing-attorney input
- Confirm facts against intake summary and any documents in the matter file
- Route to managing-attorney review per office supervision model before any client communication or filing relies on the memo's conclusions
```

## Cross-skill propagation

- The memo is logged to the matter's case file. `/status` references it. `/case-transfer` includes it in the handoff.
- Research gaps surfaced here become candidates for `/research-start` invocations.
- The completed memo informs subsequent `/draft` invocations on the matter — the legal theory in the memo is what the draft's `[STAFF REVIEW]` markers expect.
- A live funder-restriction analysis in this memo blocks `/draft` from filing-ready output until the managing attorney resolves it.

## What this skill does NOT do

- Conduct legal research. Flags the gaps; the staffer researches.
- Apply the rule to the facts. Prompts the staffer; the staffer analyzes.
- Reach a conclusion. The conclusion is the staffer's, reviewed by the managing attorney.
- State the rule. Rule blocks are `[RESEARCH NEEDED]` — the skill does not pretend to know the controlling rule in the relevant jurisdiction.
- Cite authorities. Citations come from research, not from memory; the skill suggests areas to look but never asserts a cite.
- Resolve funder-restriction questions. Surfaces and structures the analysis; the managing attorney decides whether to proceed, limit, or refer.
- Substitute for the managing attorney's review. The memo's structure makes the staffer's analysis legible for review; review is still required.
