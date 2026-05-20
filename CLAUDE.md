<!--
CONFIGURATION LOCATION

User-specific configuration for this plugin lives at a version-independent path that survives plugin updates:

  ~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md

Rules for every skill, command, and agent in this plugin:
1. READ configuration from that path. Not from this file.
2. If that file does not exist or still contains [PLACEHOLDER] markers, STOP before doing substantive work. Say: "This plugin needs setup before it can give you useful output. Run /legal-aid:cold-start-interview — it takes about 15-20 minutes and every command in this plugin depends on it. Without it, outputs will be generic and may not match how your office actually works." Do NOT proceed with placeholder or default configuration. The only skills that run without setup are /legal-aid:cold-start-interview itself and any --check-integrations flag.
3. Setup and cold-start-interview WRITE to that path, creating parent directories as needed.
4. On first run after a plugin update, if a populated CLAUDE.md exists at the old cache path
   (~/.claude/plugins/cache/claude-for-legal/legal-aid/<version>/CLAUDE.md for any version)
   but not at the config path, copy it forward to the config path before proceeding.
5. This file (the one you are reading) is the TEMPLATE. It ships with the plugin and shows the structure the config should have. It is replaced on every plugin update. Never write user data here.

**Shared company profile.** Office-level facts (who you are, what you do, where you operate, funding sources, key people) live in `~/.claude/plugins/config/claude-for-legal/company-profile.md` — one level above this file, shared by all plugins in the suite. Read it before this plugin's practice profile.
-->

# Civil Legal Aid Practice Profile

*Written by the managing-attorney-facing cold-start interview. Staff don't edit this — they run `/onboard`. If you see `[PLACEHOLDER]` below, run `/legal-aid:cold-start-interview`.*

---

## Who's using this

**Role:** [PLACEHOLDER — Managing attorney / Executive Director (default, required to run setup) | Staff attorney | Paralegal | Intake specialist | Volunteer attorney]

Setup must be run by a managing attorney or person with equivalent authority over the office's intake and supervision practice. Other staff onboard via `/legal-aid:onboard`. Clients are not plugin users — they are the people the office serves, and their materials flow through staff and attorney outputs rather than through direct plugin use.

**Managing attorney(s):** [PLACEHOLDER — name(s), bar admission jurisdiction(s), bar number(s)]
**Supervisory authority basis:** [PLACEHOLDER — e.g., "ED + Director of Litigation; managing attorney for each practice unit"]
**Ethical preconditions confirmed:** [PLACEHOLDER — yes / no; list unresolved items if any. Captured from Part 0 ethical preconditions.]
**Funder restrictions acknowledged:** [PLACEHOLDER — list each funding source whose restrictions the managing attorney has confirmed staff understand: LSC 45 CFR Part 1600, HUD, VAWA, state IOLTA, specific foundation grants, etc.]

When the role is managing attorney, staff attorney, paralegal, or intake specialist, every output this plugin produces is supervised staff work. The AI-assisted draft label is the canonical header for staff outputs in this environment — it replaces a generic privilege / non-lawyer notice.

**Consequential-action note:** Sending a client letter, filing with a court or agency, closing a case, and making representation decisions are gated by the office's supervision workflow (see `## Supervision style` below). The Part 0 role check reinforces that gate. Do not bypass the supervision workflow even when the plugin's internal checks pass.

---

## Available integrations

| Integration | Status | Fallback if unavailable |
|---|---|---|
| Case management system (Legal Server / LegalFiles / Clio / PIKA / JusticeServer) | [✓ / ✗ / which one] | Case metadata captured in local intake / status files; no auto-sync |
| Document storage (Google Drive / SharePoint / Box) | [✓ / ✗] | Staff outputs save to local filesystem; review stays in-plugin |
| Intake-routing connector (provider-specific, optional) | [✓ / ✗ / which one] | `/eligibility-screening` and `/client-intake` use built-in templates without external triage signal |
| Procedural-retrieval connector (provider-specific, optional) | [✓ / ✗ / which one] | Citations tagged `[model knowledge — verify]` rather than `[provider — pinned]` |

*Re-check: `/legal-aid:cold-start-interview --check-integrations`*

---

## Office profile

**Office:** [PLACEHOLDER — name] *(From company-profile.md)*
**Type:** [PLACEHOLDER — LSC-funded legal services / non-LSC civil legal aid / hybrid / court self-help center / public defender civil division / pro bono coordinator office]
**Practice areas:** [PLACEHOLDER — housing / family / consumer / immigration / public benefits / civil rights / elder / DV / other] *(From company-profile.md)*
**Funding sources:** [PLACEHOLDER — LSC / IOLTA / HUD / VAWA / VOCA / state appropriation / foundation / private]
**Staff size:** [PLACEHOLDER — attorneys / paralegals / intake specialists]
**Typical annual intake volume:** [PLACEHOLDER]
**Typical active caseload per attorney:** [PLACEHOLDER]

**Client population:** [PLACEHOLDER — who walks in / calls in, common situations]
**Languages beyond English:** [PLACEHOLDER]
**Common referral sources:** [PLACEHOLDER]
**Common referral destinations (for declined cases):** [PLACEHOLDER — private bar referral, court self-help center, lawyer-of-the-day, mediation program, etc.]

---

## Jurisdiction

**State(s):** [PLACEHOLDER] *(From company-profile.md)*
**Counties served:** [PLACEHOLDER]
**Primary courts:** [PLACEHOLDER — district / county / housing / family / immigration]
**Local rules ingested:** [PLACEHOLDER — list files, or "none yet — /draft will use state defaults and flag"]

---

## High-volume deadlines (plausibility check)

*Captured at cold-start. Used by `/legal-aid:deadlines --add` to sanity-check deadline entries — gross arithmetic errors or date-entry typos get flagged for re-check. Not a computation tool; not authority. The skill does not compute deadlines from triggering events; this section catches obvious entry mistakes only. Every cite below carries an implicit `[VERIFY]` — statutes get amended, and these ranges are operational shortcuts, not legal advice.*

| Deadline type | Triggering event | Typical days-out | Source / authority |
|---|---|---|---|
| [PLACEHOLDER — captured at cold-start, or skipped] | | | |

*If skipped at cold-start, `/legal-aid:deadlines --add` will not perform plausibility checks but still tracks deadlines, warns at 14/7/3/1 days, and surfaces overdue. Add entries by editing this section directly, or run `/legal-aid:cold-start-interview --redo plausibility-bands`.*

---

## Eligibility guidelines

*The shape of eligibility your office applies. `/eligibility-screening` reads this section. Do not encode bright-line numbers that change annually here — point to the source the office tracks (LSC poverty guidelines table, state IOLTA rules, funder-specific income tables) and let the skill flag `[VERIFY: current FPL table]` at runtime.*

**Income standard:** [PLACEHOLDER — e.g., "125% FPL for LSC-funded matters; 200% FPL for IOLTA-funded matters; 300% FPL for elderly (60+) and DV survivors per state IOLTA rules"]
**Asset limits:** [PLACEHOLDER — e.g., "Excludes primary residence and one vehicle; $2,500 liquid asset cap for LSC-funded; no cap for DV"]
**Residency requirements:** [PLACEHOLDER]
**Citizenship / immigration status policy:** [PLACEHOLDER — describe the LSC alien restrictions and exceptions your office applies; or describe non-LSC policy if applicable]
**Conflict-check process:** [PLACEHOLDER — opposing party, related parties, positional conflicts; CMS-backed check or manual]
**Priority case types:** [PLACEHOLDER — e.g., "Emergency eviction defense; DV protective orders; benefits termination appeals; consumer judgments with execution pending"]
**Out-of-scope case types:** [PLACEHOLDER — e.g., "Criminal defense, personal injury, fee-generating matters, class actions (LSC restricted)"]

---

## Supervision style

*The managing attorney chose one of three models at setup. This determines how staff output is reviewed before going to clients or courts.*

**Model:** [PLACEHOLDER — "formal review queue" | "configurable flags, informal review" | "lighter-touch"]

**If formal queue or configurable flags — triggers:**
- [PLACEHOLDER — e.g., "Any court filing"]
- [PLACEHOLDER — e.g., "Any substantive advice letter"]
- [PLACEHOLDER — e.g., "Any deadline mentioned"]
- [PLACEHOLDER — e.g., "DV / immigration status / criminal exposure indicators"]
- [PLACEHOLDER — e.g., "Any LSC funding-restriction flag"]

**What each model means in practice:**
- **Formal review queue:** Staff output that's client-facing or court-bound queues. Managing attorney approves/edits/returns. Logged. (`managing-attorney-review` skill active.)
- **Configurable flags:** Triggers above produce "CHECK WITH MANAGING ATTORNEY" labels. No queue mechanism — staff responsible for checking in. (`managing-attorney-review` skill dormant.)
- **Lighter-touch:** Standard AI-assisted label + verification prompts on everything. No additional gates. Managing attorney supervises through case rounds, one-on-ones, existing office structure.

---

## Practice-area templates

*Documents `/draft` knows how to start. Populated at cold-start; add more by editing here or uploading templates.*

### [Practice area 1]

**Intake template:** [PLACEHOLDER — path or "default questions"]
**Common documents:**
| Document | Template | Notes |
|---|---|---|
| [PLACEHOLDER] | [path or "build from scratch"] | |

### [Practice area 2]

[same structure]

---

## Funder reporting

*What `/lsc-report` and the per-funder reporting flows need to know.*

**LSC reporting:** [PLACEHOLDER — yes/no; if yes, list CSR categories used and any office-specific subcategories]
**IOLTA reporting:** [PLACEHOLDER]
**Other funder-specific reports:** [PLACEHOLDER — VAWA quarterly, HUD HOC, foundation milestone reports, etc.]
**Reporting cadence:** [PLACEHOLDER]
**Reporting owner:** [PLACEHOLDER — name / role]

---

## Output safeguards

Every output from every skill includes the safeguards listed in the README (`## Confidence markers`). Skill authors: do not omit these. The shared guardrails in this plugin are:

1. **AI-assisted label on every staff output** — strip before sending; review label only
2. **Provenance tags on every citation** — `[source — tag]` per the README vocabulary
3. **Verify-user-stated-legal-facts** — when a client or referring agency asserts a legal fact (status, prior order, deadline), the skill that uses it flags it `[VERIFY: client-stated — confirm against source]` rather than treating it as established
4. **Premise verification** — the skill names its operating premises explicitly so the reviewer sees what the output rests on
5. **Destination check** — every output names its audience (client / court / funder / internal) so the reviewer doesn't accidentally send funder-facing language to a client
6. **Funding-restriction flag** — `[FUNDING RESTRICTION: ...]` whenever a contemplated action plausibly engages LSC 45 CFR Part 1600 or another funder restriction the office has flagged in this profile
7. **Cross-skill severity floor** — a flag raised in one skill propagates into the file; a `[FUNDING RESTRICTION]` flagged in intake is repeated in the draft, memo, and status that follow

---

## How this learns

Each skill reports when it used a default the managing attorney should tune. Common signals:

- "I assumed [X] because the profile didn't specify — should I add this to the profile?"
- "I used the generic intake because there's no guide for [practice area]. Want to author one with `/build-guide`?"
- "The conflict check ran with the manual process because there's no CMS connector — set up the connector or update the profile's conflict-check description."

Edit this file directly to tune. Re-run setup with `--redo` to walk through the interview again.
