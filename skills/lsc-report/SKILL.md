---
name: lsc-report
description: >
  Funder reporting data extraction. Reads the office's funder reporting
  cadence and format from CLAUDE.md, queries the case-management system
  (or instructs on manual extraction), and produces a data package
  matching each funder's expected format — LSC CSR semi-annual, IOLTA
  narrative, VAWA quarterly, HUD reports, foundation milestones, state
  appropriation reports. Does NOT submit. Generates the data package
  the office's reporting workflow consumes. Use at reporting cadence,
  ad-hoc when a funder requests data, or to dry-run a reporting cycle.
argument-hint: "[funder=lsc|iolta|vawa|hud|foundation|state] [period=2026-Q1|2025-H2|...] [--dry-run]"
---

# /lsc-report

1. Load `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → funder reporting section, funders applied, reporting cadence, ownership, format pointers, sample-report path.
2. Identify the funder and period. Default to the next-due report if not specified.
3. If a case-management system is connected, query for closed and active cases matching the funder's reporting criteria. If not, output the structured query the reporting owner should run manually.
4. Map case-management data to the funder's report format (LSC CSR categories, IOLTA narrative shape, etc.).
5. Identify gaps — cases that should be in the report but lack required fields; cases that may have been miscoded.
6. Produce the data package, matched to the office's prior-report format if a sample was uploaded.
7. Output with AI-assisted label, gap list, and instructions for the reporting owner.

```
/legal-aid:lsc-report funder=lsc period=2026-H1
/legal-aid:lsc-report funder=iolta period=2025-annual
/legal-aid:lsc-report --dry-run
```

---

# Funder Reporting

## Purpose

Funder reporting is the back end of the legal aid funnel. The same office that runs eligibility screening, intake, and case management at the front end has to extract, format, and submit data to LSC, IOLTA, HUD, VAWA, state appropriations, and foundation grantors at funder-specific cadences. Each report has its own format, categories, narrative requirements, and quirks. Getting it wrong puts funding at risk.

This skill reads the office's reporting configuration from CLAUDE.md, queries the case-management system for the data each funder wants, maps that data to the funder's expected format, flags gaps and miscoded entries, and produces a data package matching the office's prior reporting style. The reporting owner (managing attorney, grants person, or designated staffer) takes that package, reviews it, and runs the actual submission through the office's normal workflow.

**What it doesn't do:** submit. Submission to LSC's CSR system, the state IOLTA portal, HUD, the foundation's reporting interface — those run through the office's authenticated session, not the plugin. The plugin extracts and formats; the reporting owner submits.

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → funders applied (from Part 0 of cold-start), funder-reporting section (cadence, ownership, case-management data feeding each report, office-specific subcategories, narrative templates), sample-report paths if uploaded at cold-start.

If the office didn't upload a sample report for the requested funder at cold-start, the skill falls back to a generic template and notes the gap. Match-to-prior-format is the default; generic-template is the fallback.

## Funder shapes

Different funders want different things. The skill recognizes these shapes and adapts:

### LSC (Legal Services Corporation) — Case Service Reports

- **Cadence:** Semi-annual (H1 and H2), submitted via LSC's Grants Portal
- **Unit of report:** Closed cases in the period
- **Categorization:** CSR Categories 1-9 (Counsel and Advice / Brief Services / Other / Negotiated Settlement Without Litigation / Negotiated Settlement With Litigation / Agency Decision / Court Decision / Extensive Service / Other)
- **Required fields per case:** Case type (broad), problem code (LSC's taxonomy), funding source allocation, hours allocated, demographic data (race, ethnicity, gender, age range, household income, household size, language)
- **Office-specific subcategories:** As captured in CLAUDE.md
- **Aggregate report:** Total cases closed, totals by category, demographic aggregates

### IOLTA — Narrative + quantitative

- **Cadence:** Typically annual; varies by state
- **Format:** Combination of narrative (program description, achievements, challenges) and tables (cases handled, demographics, geographic spread, funding sources)
- **Office-specific:** State IOLTA programs have different requirements; the office's prior report shows the actual shape

### VAWA — Quarterly

- **Cadence:** Quarterly
- **Focus:** Cases serving DV/sexual assault/stalking/trafficking victims under VAWA-funded activities
- **Required:** Service counts, demographic data, geographic, type of services rendered (legal advice, brief services, full representation, court accompaniment), confidentiality compliance attestation
- **Confidentiality:** VAWA imposes specific data-handling restrictions; the report aggregates rather than discloses individual case details

### HUD — Project-specific

- **Cadence:** Varies by grant (annual, quarterly, milestone)
- **Focus:** Housing-related cases, often subsidy preservation, fair housing, or specific HUD-funded program populations
- **Format:** Per-grant; office uploads sample at cold-start

### Foundation grants — Milestone-based

- **Cadence:** Per grant terms (often annual or per milestone)
- **Format:** Foundation-specific; some standardized via Form 990 / Foundation Center conventions, most bespoke
- **Office uploads sample** at cold-start

### State appropriation — Statewide bar foundation or equivalent

- **Cadence:** Annual typically
- **Format:** Per state; some align with LSC categories, others use state-specific taxonomies

## Workflow

### Step 1: Identify the report

Confirm funder and period. If not specified:

> Which funder report? The next reports due per your cadence are:
> - LSC: [2026-H1 CSR — due July 31, 2026]
> - IOLTA: [2025 annual — due April 30, 2026 (overdue)]
> - VAWA: [2026-Q1 — due April 30, 2026]
> - [other funders per CLAUDE.md]
>
> Or specify with `funder=` and `period=`.

If a report is overdue, flag prominently. If the office is missing the configuration for a funder named in Part 0 of cold-start, route to `/cold-start-interview --redo reporting` to populate it.

### Step 2: Define the case population

Query criteria for the funder and period:
- **LSC CSR:** All cases *closed* in the period that were funded (in whole or part) by LSC funds. Note: a case run on mixed LSC + non-LSC funds reports the LSC-funded portion only.
- **IOLTA:** Cases funded by IOLTA in the period — closed cases for the case-count section; all served clients for the demographic section.
- **VAWA:** Cases funded by VAWA in the period, restricted to DV/SA/stalking/trafficking categories.
- **HUD:** Per grant — usually the project-specific case population.
- **Foundation / state:** Per grant.

Output the query criteria in plain language so the reporting owner can verify before running.

### Step 3: Query (or instruct on querying)

If the case-management system is connected via MCP:
- Run the query against the funder's criteria
- Retrieve required fields per case
- Note any cases returning incomplete data

If not connected:
- Output the structured query for the reporting owner to run manually against their CMS (Legal Server, LegalFiles, Clio, PIKA, JusticeServer)
- Provide the field list each case should return
- Wait for the reporting owner to provide the data (paste, upload, or describe)

### Step 4: Map to funder format

For each case in the population:
- Map case-management fields to funder-required fields
- Apply LSC's problem-code taxonomy / IOLTA's categories / VAWA's service taxonomy as applicable
- Aggregate demographic data per funder's required shape
- Compute totals, percentages, geographic distributions

Use the office's prior report (uploaded at cold-start) to match format precisely — section headings, table structures, narrative conventions. If no prior report uploaded, use the funder's official template and note: "Format inferred from funder template; office prior report not available."

### Step 5: Gap and miscoding analysis

Flag for the reporting owner:
- **Missing required fields:** Cases that should be reportable but lack data the funder requires (e.g., demographic fields blank, problem code unset, funding allocation undocumented)
- **Possible miscoding:** Cases coded under one funder that based on facts may belong under another (e.g., a housing matter coded LSC but with characteristics suggesting it engaged § 1626 restrictions and should have been allocated to IOLTA)
- **Boundary cases:** Cases closed near the period boundary where the "closed in period" classification may be debatable
- **Confidentiality flags:** For VAWA specifically, any case where the data extracted could compromise the confidentiality requirements of 42 USC § 13925(b)(2) — flag for the reporting owner to handle aggregation manually

These flags are surfaced; the reporting owner resolves them.

### Step 6: Output the data package

## Output

```markdown
# [Funder] Report Data Package: [Period]

---
[AI-ASSISTED DATA PACKAGE — requires reporting-owner review and managing-attorney sign-off before submission]

**Confidentiality.** This package contains aggregated and case-level data subject to office privilege rules, funder-specific confidentiality requirements (VAWA, HUD), and Rule 1.6. Store in the office's privileged file location. Distribution restricted to reporting owner and managing attorney until submission is approved.
---

**Funder:** [name] | **Period:** [period] | **Generated:** [date]
**Format basis:** [matched to office prior report uploaded [date] / generic funder template]
**Reporting owner:** [name from CLAUDE.md]
**Due date:** [date]

## Summary

- Cases in report population: [N]
- Cases with complete data: [N — N% of population]
- Cases with gaps requiring resolution: [N — see Gaps section]
- Possible miscoding flags: [N — see Miscoding section]

## Aggregate sections

[Funder-specific aggregate tables and narratives, matched to office prior format:]

[For LSC CSR:]
| CSR Category | Count | % |
|---|---|---|
| 1. Counsel and Advice | [N] | [pct] |
| 2. Brief Services | [N] | [pct] |
| ... | ... | ... |

[Demographic aggregate]
[Problem-code aggregate]

[For IOLTA: narrative + tables per office prior]
[For VAWA: service counts + aggregated demographic + confidentiality attestation prompt]
[etc.]

## Case-level data (where required)

[Funder-required case-level rows. For LSC CSR, this is per case with the LSC-required fields. For VAWA, aggregated only — no case-level data leaves the privileged store.]

[Table or structured list per funder format]

## Gaps requiring resolution before submission

- [Case ID] [Field missing] [Suggested action — e.g., "Demographic fields blank; check intake form for race/ethnicity; if not collected, document why per office's data-collection policy"]
- [Case ID] [Field missing] [...]

## Possible miscoding flags

- [Case ID] [Issue — e.g., "Coded LSC; facts suggest § 1626 alien restriction engaged; may belong under IOLTA. Recommend managing-attorney review."]
- [Case ID] [Issue — ...]

## Boundary cases

- [Case ID] [Close date / opening date relative to period — e.g., "Closed [date] — within 2 days of period boundary. Verify closure date is correct."]

## Confidentiality flags (VAWA only)

- [Case ID] [Concern — e.g., "Demographic aggregation with N=2 in this rural county may risk identifying clients. Suppress or further aggregate."]

## Narrative sections (where required)

[For IOLTA / foundation / state reports with narrative requirements:]

**Program description:** [Pulled from CLAUDE.md office profile; reporting owner edits as appropriate]

**Achievements this period:** [Drawn from closed-case outcomes; reporting owner reviews and edits]
- [Achievement summary 1]
- [Achievement summary 2]

**Challenges this period:** [Reporting owner authors; AI suggests prompts based on intake volume vs. capacity data]
- [Suggested prompt for reporting owner]

**Looking ahead:** [Reporting owner authors]

## Premises this package rests on

- [Data source(s) and query date]
- [Case-management system data as of [timestamp]]
- [Office prior report format reference, if used]
- [Funder template reference and version, if used as fallback]

## Verification prompts before submission

- Verify aggregate totals against case-management system independently — match the count in the report to the count in the system
- Resolve every gap and miscoding flag with the managing attorney
- Verify demographic aggregates against the office's data-collection policy and any consent limitations
- For VAWA: review every confidentiality flag and aggregate further if any case-level disclosure risk exists
- For LSC: confirm hours allocation matches the office's time-tracking system, not estimated from case count
- Confirm narrative sections are factually accurate and approved by the managing attorney before submission
- Save final-version package to the privileged file store with the submission acknowledgement

## Submission notes (from CLAUDE.md)

- Submission method: [LSC Grants Portal / state IOLTA portal / foundation portal / email to grants officer]
- Submission credentials: [held by reporting owner — not in this package]
- Submission deadline: [date]
- Office's typical submission window before deadline: [e.g., "2 weeks before deadline"]
```

## `--dry-run`

Run all steps except the final data package output. Show:
- Which funders / periods are next due
- Which CMS queries would run
- Which CLAUDE.md fields would be consulted
- What gaps and miscoding flags would surface based on current case data

Useful for verifying configuration before a real reporting cycle, or for the managing attorney to see what the next quarter's report will look like.

## Cross-skill propagation

- The funder-source matrix that propagates through `/eligibility-screening` → `/client-intake` → `/draft` etc. is the *input* this skill aggregates from. Skills that fail to propagate the matrix create gaps that surface here.
- Cases flagged with `[FUNDING RESTRICTION: ...]` in earlier skills should match the funder allocation in case management; mismatches surface as miscoding flags.
- This skill's *output* is not consumed by other skills directly — it goes to the reporting owner's submission workflow. But the gaps and miscoding flags should propagate back to case-level work product: a miscoded case may need re-coding in the case-management system, and that's a managing-attorney decision.

## What this skill explicitly does not do

- Submit to any funder. Submission runs through the office's authenticated workflow.
- Make funder-allocation decisions. The funder allocation on a case is set at intake/acceptance and during case work; this skill reports what was set, with flags for possible errors.
- Override confidentiality requirements. VAWA in particular imposes strict data-handling rules; the skill aggregates conservatively and surfaces edge cases for the reporting owner.
- Generate the office's narrative answers from scratch. For reports requiring narrative, the skill drafts prompts and pulls outcome data; the reporting owner and managing attorney author the actual narrative.
- Audit the case-management system. If the underlying CMS data is wrong, this skill produces a wrong report. Gaps and miscoding flags help surface the worst cases, but the report is only as good as the data that fed it.
- Decide how to handle gaps. Surfaces them; the managing attorney decides whether to delay submission, submit with caveats, or proceed.
