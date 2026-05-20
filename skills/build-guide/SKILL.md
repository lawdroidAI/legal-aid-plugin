---
name: build-guide
description: >
  Help a managing attorney author a per-practice-area guide that configures
  how staff-facing skills behave — intake questions specific to this
  practice area, escalation posture (handle / escalate / refer), review
  gates, funder-restriction overrides, cross-area watch list, local rules,
  and practice-area draft templates. Use when a managing attorney wants to
  build or revise a per-practice-area guide, tune how the skills behave for
  a specific practice area, or set the office's escalation philosophy as
  plugin configuration.
argument-hint: "[optional: practice area — e.g., 'housing', 'family', 'immigration']"
---

# /build-guide

1. Load `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → role (must be Managing attorney / ED), practice areas, jurisdiction, supervision style, funder restrictions.
2. Use the workflow below.
3. If the user is not the managing attorney, stop and redirect (staff run `/legal-aid:onboard`).
4. Walk through: practice area → intake questions → escalation posture → review gates → funder-restriction overrides → cross-area watch list → local rules → draft templates.
5. Write `~/.claude/plugins/config/claude-for-legal/legal-aid/guides/<practice-area>.md`. Create the `guides/` directory if needed.
6. Offer a test run — execute `/legal-aid:client-intake` or `/legal-aid:draft` under the configured posture so the managing attorney sees what a staffer sees.

```
/legal-aid:build-guide
/legal-aid:build-guide housing
```

Multiple guides are fine — one per practice area. Re-run to revise. Edit the guide file directly for quick changes.

---

# Build Guide: Managing-Attorney-Authored Practice-Area Guide

## Purpose

The `legal-aid` plugin ships with sensible defaults across practice areas. But every office runs each practice area its own way: which intake questions matter, which red flags warrant immediate escalation, which funder restrictions bind hardest in this area, which cross-area issues most often co-occur, which local courts have quirks the staffer needs to know. A generic plugin handles this generically, which means the staff spend their first months correcting what the tool assumed.

This skill captures the managing attorney's specific judgment about a practice area into a guide file that every staff-facing skill reads at runtime. Output across `/client-intake`, `/draft`, `/memo`, `/research-start`, and `/status` becomes calibrated to this office's actual practice in this area, not the generic template.

**Audience: managing attorney or ED.** Staff don't run this — they run `/onboard` and then the practice skills.

## Role gate

Before starting, confirm the user is the managing attorney or ED. If not:

> This setup writes office-level configuration that affects how every staffer's work product is structured. It must be done by the managing attorney or ED. If you're a staff attorney, paralegal, or intake specialist, run `/legal-aid:onboard` to come up to speed on the office's current configuration.

If confirmed, continue.

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → office profile, practice areas list, jurisdiction(s), supervision style, escalation default, funder restrictions, conflict-check process.

If CLAUDE.md is missing or has placeholders: "Office setup hasn't been completed yet. Run `/legal-aid:cold-start-interview` first — every skill, including this one, depends on it."

## The walkthrough

### Step 1: Practice area selection

If a practice area was passed as an argument, confirm and proceed. If not, ask:

> Which practice area? Your office handles [list from CLAUDE.md]. You can also author a guide for a sub-area within a practice area (e.g., "eviction defense" within housing) if that distinction matters for your office.

If the user names a practice area the office doesn't handle per CLAUDE.md, ask whether to add it to the office's practice areas (which routes back to `/cold-start-interview --redo practice-areas`) or whether this is a one-off guide that doesn't change the office's primary scope.

### Step 2: Intake questions specific to this practice area

The `/client-intake` skill ships with generic per-practice-area templates. The office probably has additional or replacement questions that matter for the work it does. Ask:

> What are the 5-10 questions you want every intake in this practice area to surface — beyond the generic template? Think about: the facts that determine whether the matter is in scope; the facts that signal urgency; the facts that flag cross-area issues; the facts that determine which funder this matter belongs under.

For each question:
- The question text the staffer asks (or the prompt the skill uses)
- Why it matters in this office
- What answer should trigger a flag, and what kind of flag

Capture in the guide. The intake skill will append these to the generic template for this practice area.

### Step 3: Red flags for this practice area

> What in an intake or screening should set off alarm bells specifically in this practice area? Different from generic urgency markers — these are things that mean "stop, this needs the managing attorney before going further."

Examples by practice area:

- **Housing:** Lockout already occurred; child welfare involvement; subsidy-program-specific issues (Section 8 termination, public housing one-strike, LIHTC); landlord retaliation pattern
- **Family:** Active DV with imminent threat; child welfare involvement; international parental abduction; immigration status of any party
- **Consumer:** Wage garnishment active; military servicemember (SCRA); fraud or identity theft basis; bankruptcy filed by another party
- **Immigration:** Removal hearing within 30 days; ICE detention; criminal-immigration interaction; minor without guardian
- **Public benefits:** Termination effective within 14 days; overpayment claim with criminal referral; disability case with imminent medical need
- **DV:** Imminent threat indicators; firearm in household; abuser is law enforcement; protection order violation pattern

Capture each red flag with: how it's spotted, what the immediate action is, who gets pinged.

### Step 4: Escalation posture for this practice area

The office set a default escalation posture at cold-start (handle / escalate / refer). For this practice area, is the posture the same, or different?

> Your office's default escalation posture is [default from CLAUDE.md]. For this practice area, do you want the same, or different?
>
> - **Handle (skill produces work product, staff review and route):** Best for high-volume routine matters where the work is well-documented and staff are experienced.
> - **Escalate (skill produces structure, flags complex elements for managing-attorney review before continuing):** Best when the practice area mixes routine and complex matters and you want a human check before any complex element progresses.
> - **Refer (skill scopes the matter, flags as outside AI-assisted scope, routes to referral):** Best for practice areas where the office prefers low-AI-touch — typically heightened-confidentiality or high-stakes (some DV configurations, criminal-adjacent matters, certain immigration scenarios).

If different from default, capture and confirm. Some practice areas commonly run "escalate" even when the office defaults to "handle" (DV, immigration removal). Confirm explicitly.

### Step 5: Review gates

> In this practice area specifically, what triggers managing-attorney review before output goes out, beyond your office-wide supervision style?

Common practice-area-specific review gates:
- Housing: any motion for stay of execution, any settlement involving an admission
- Family: any motion involving custody, any settlement involving parenting time
- Consumer: any settlement, any agreement to a payment plan
- Immigration: any filing involving the executive (asylum, withholding), any declaration
- DV: any pleading mentioning the abuser by name, any disclosure of safe address
- Public benefits: any administrative-hearing-day filing

Capture in the guide. These overlay on top of the office's general supervision style — they're additive, not replacement.

### Step 6: Funder-restriction overrides for this practice area

For each funder named in Part 0 of cold-start, ask whether any funder-restriction applies specifically to this practice area in a way that should change skill behavior:

> Does any funder restriction bite particularly hard in this practice area? Examples: LSC § 1626 alien restrictions hit immigration matters most directly; LSC class-action restrictions hit civil rights matters; LSC fee-generating restrictions hit consumer matters; VAWA confidentiality requirements hit DV matters; HUD funding may restrict housing matters to specific subsidy contexts.

For each restriction-area pairing identified, capture:
- The funder + restriction
- How it manifests in this practice area
- What flag the skills should raise, and at what point in the workflow
- What the default routing is (escalate, refer, document and proceed)

### Step 7: Cross-area watch list

> What other practice areas most often co-occur with this one? An intake in this practice area should surface possible cross-area issues — what should the skill be listening for?

Examples by primary area:
- **Housing** → DV (residence dispute often involves), public benefits (subsidy implications), consumer (utility shutoff), family (custody affected by housing instability)
- **Family** → DV, immigration (status of any party), public benefits (child support reporting), housing (parental relocation)
- **Consumer** → bankruptcy (already filed?), public benefits (exempt income), employment (wage garnishment)
- **Immigration** → DV (VAWA / U / T visa pathways), family (status of children), public benefits (eligibility implications)
- **Public benefits** → housing (subsidy interaction), family (child support reporting), consumer (collection of benefits)
- **DV** → family (custody, divorce), housing (lease termination, safe address), immigration (VAWA, U visa)

Capture the watch list. The intake skill uses it to flag cross-area issues in this practice area's intakes.

### Step 8: Local rules and quirks

> What does a staffer need to know about how this practice area runs in your jurisdiction(s) that isn't in the generic plugin or in your CLAUDE.md local-rules ingest?

Examples:
- Specific judge preferences ("Judge X does not accept agreed orders without a hearing on the record")
- Court office quirks ("File pro se housing answers at window 3, not the regular filing window")
- Agency conventions ("The state SNAP fair-hearing scheduling office responds faster to emailed requests than fax")
- Local bar pro bono panel referrals ("Above 175% FPL family matters refer to the Wake County Bar pro bono panel")

Capture as a local-knowledge appendix to the guide. The relevant skills surface these in their outputs when the practice area matches.

### Step 9: Draft templates for this practice area

The plugin's `/draft` command works from practice-area templates. What documents does staff draft most often in this practice area?

Confirm the 3-8 documents already in CLAUDE.md, then ask:
- Are there office-specific templates the office uses that aren't in the generic plugin?
- Are there local-jurisdiction templates (specific to one county, one court, one judge)?
- Should any common document have a per-funder variant (e.g., a different demand letter for LSC-funded matters vs IOLTA-funded)?

For each template, ask the managing attorney to upload an example. The `/draft` command will use these as starting points for first drafts in this practice area.

## Writing the guide

Write to `~/.claude/plugins/config/claude-for-legal/legal-aid/guides/<practice-area>.md`. Structure:

```markdown
# [Practice area] guide

*Written by [managing attorney name] on [date]. Read at runtime by `/client-intake`, `/draft`, `/memo`, `/research-start`, and `/status` whenever the matter's practice area matches.*

## Intake questions (additional to generic template)

[per Step 2]

## Red flags

[per Step 3]

## Escalation posture

[handle / escalate / refer, with reason if different from office default]

## Review gates

[per Step 5]

## Funder-restriction overrides

[per Step 6]

## Cross-area watch list

[per Step 7]

## Local rules and quirks

[per Step 8]

## Draft templates

[per Step 9, with paths to uploaded exemplars]
```

## After writing

1. Show the managing attorney the file as written and confirm.
2. Offer a test run: "Want to see what a staffer sees? I can run `/legal-aid:client-intake` or `/legal-aid:draft` against a hypothetical matter so you can see the configured behavior."
3. Note that the guide takes effect immediately for new matters in this practice area; existing in-flight matters continue with whatever posture they started under.

## What this skill does NOT do

- Change office-level configuration. CLAUDE.md governs office-wide settings; this skill writes a guide for one practice area. To change office-wide settings, run `/legal-aid:cold-start-interview --redo`.
- Author legal substance. The skill captures procedural and configuration judgment, not doctrine. The doctrine is in the staffer's research and the managing attorney's review.
- Validate the practice area against state law. The managing attorney is responsible for ensuring the configured guide is accurate for the jurisdictions the office operates in.
- Run for non-managing-attorney users. Staff who attempt to run this get redirected to `/onboard`.
