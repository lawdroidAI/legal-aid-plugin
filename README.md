# Legal Aid Plugin

*Supercharging access to justice through AI-enabled civil legal aid practice.*

![Logo](https://legalaidplugin.org/images/legal-aid-plugin-logo.png)

A plugin for civil legal aid organizations, court self-help centers, and public-interest legal services providers — the staffed nonprofits that handle housing, family, consumer, immigration, public benefits, and civil rights matters for people who cannot afford counsel.

**Every output is a draft for staff analysis and managing-attorney review: marked, gated, and logged. The plugin scaffolds the work; staff reason through it; a managing attorney reviews. Nothing leaves the office without going through the supervision model the organization set at setup.**

## The problem the Legal Aid Plugin solves

Civil legal aid is structurally capacity-constrained. A managing attorney supervises 5–20 staff. Each staff attorney carries 40–80 active matters. Intake volume routinely exceeds capacity by 3x to 5x. Eligibility screening, intake write-up, first drafts, status updates, referral letters, court self-help triage — the administrative load consumes hours that could go to advising clients. The result: long intake queues, declined cases, people who give up waiting.

This plugin cuts the time cost of everything *around* the lawyering, so the same staff and managing attorney serve meaningfully more clients, and staff spend more time on the analysis and strategy that actually moves cases.

**It accelerates the non-analytical parts. It preserves the lawyering.** That's the design principle.

## Who uses it

| Role | Runs | Gets |
|---|---|---|
| **Executive Director / Managing attorney** | `/cold-start-interview` (once), `/build-guide` (per practice area), `/managing-attorney-review` (if formal review enabled) | Office context configured, staff work reviewed |
| **Staff attorneys & paralegals** | `/onboard` (joining staff), then `/eligibility-screening`, `/client-intake`, `/draft`, `/memo`, `/research-start`, `/status`, `/client-letter` | Starting points — never final work product |
| **Intake specialists** | `/eligibility-screening`, `/client-intake`, `/client-comms-log` | Structured screening + referral leads |

## Commands

| Command | What it does | What it doesn't do |
|---|---|---|
| `/cold-start-interview` | **ED/Managing attorney.** One-time office config: funding sources, practice areas, eligibility guidelines, jurisdictions, supervision style, conflict-check process | — |
| `/build-guide` | **Managing attorney.** Author a per-practice-area guide: intake questions, escalation posture (handle / escalate / refer), review gates, cross-practice checks | Doesn't replace `/cold-start-interview` — this tunes skills for one practice area |
| `/onboard` | **New staff.** Onboarding flow: office procedures, tool walkthrough, jurisdiction-specific basics, practice exercises | Doesn't replace the office's formal training |
| `/eligibility-screening` | Funding-source-aware screening: income, assets, residency, citizenship/immigration status, conflicts, case-type priority | Doesn't decide acceptance — produces a recommendation for managing-attorney review |
| `/client-intake` | Structured intake after eligibility clears: practice-area templates, cross-area issue spotting, conflict flags, urgency triage | Doesn't decide whether to take the case |
| `/draft [doc]` | First draft: eviction answers, protective orders, demand letters, benefits appeals, asylum apps — jurisdiction-aware | Doesn't produce final work product |
| `/memo` | IRAC-scaffolded case analysis with research gaps flagged | Doesn't write the analysis — scaffolds it |
| `/research-start [issue]` | Research roadmap: statutes, case law areas, agency guidance, search terms | **Leads, not authoritative citations** — verify everything |
| `/status [audience]` | Case status summary: client-facing, internal, court-ready, funder-ready | Doesn't file anything |
| `/client-letter [type]` | Routine correspondence: appointment confirms, doc requests, brief updates, referral letters | Doesn't do substantive advice — that's `/status client` or a conversation |
| `/deadlines` | Track case deadlines — add, cross-case rollup with warnings at 14/7/3/1 days, overdue flags | Doesn't calculate deadlines from triggering events; staff do the math per local rules |
| `/client-comms-log [case]` | Append-only per-case communication log | Doesn't store substantive legal analysis; comm record only |
| `/case-transfer` | Per-case handoff memo for rolling staff transitions (departure, role change, leave coverage) | Doesn't close cases; cases closing get a final `/status internal` memo and are marked closed in the transfer document |
| `/managing-attorney-review` | **Managing attorney, if formal review enabled.** What's waiting, approve/edit/return | Optional — one of three supervision models |
| `/lsc-report` | **Managing attorney.** Extract case-management data for LSC CSR, IOLTA reports, and funder-specific reporting | Doesn't submit — generates the data for the org's reporting workflow |

## Ethical and confidentiality preconditions

Before using this plugin with real client matters, confirm with your office's managing attorney and your IT/compliance lead:

1. **Your Claude account tier and its data retention and training policies.** Team, Enterprise, Work, and individual accounts have different guarantees about retention, training use, and subprocessor handling. Confirm what applies to the office's account, and confirm it satisfies your funder's data-handling requirements (LSC, HUD, VAWA, state IOLTA, etc.).
2. **Your client consent and disclosure practices for AI-assisted work** per ABA Formal Opinion 512 (2024), your state bar's AI guidance (if any), and Model Rules 1.1, 1.4, 1.6, and 5.3. Decide whether and how the office discloses AI use to clients; document it.
3. **How privileged and confidential material will be handled** — what gets pasted into sessions, where outputs are stored, who has access, how long material is retained, how staff turnover affects access.
4. **Whether any of your practice areas involve heightened confidentiality** (immigration, criminal-adjacent, domestic violence, some family and civil rights matters) that require additional safeguards — and decide whether the plugin is appropriate for those case types at all.
5. **LSC and funder restrictions on AI use, if any.** LSC-funded programs operate under 45 CFR Part 1600 program rules and specific restrictions (class actions, fee-generating cases, certain alien populations, prisoner litigation, etc.). The plugin does not enforce these rules — your office does. Make sure the people running the plugin know which restrictions apply.

Do not skip this step. The cold-start interview (`/legal-aid:cold-start-interview`) captures these decisions as Part 0 before any other configuration.

## Confidence markers

Skills across this plugin flag confidence inline so staff and managing attorneys can see where the scaffold is uncertain vs. where it's asserting. Every marker is a prompt to verify — nothing marked is trusted.

- `[AI-ASSISTED DRAFT — requires staff analysis and managing-attorney review]` — baseline label applied to every output. Review label, not part of client-facing content; strip before anything goes out.
- `[UNCERTAIN: specific reason]` — the skill is genuinely unsure on this call. Used in memo, intake, status, draft.
- `[VERIFY: claim — check source]` — a claim stated as likely but unverified. Staff must confirm before relying. Used heavily in research-start, draft, status, memo.
- `[RESEARCH NEEDED: ...]` — memo scaffold marker where a rule statement is a research gap, not a conclusion.
- `[STAFF ANALYSIS: ...]` — memo scaffold marker where the application is blank by design.
- `[STAFF CONCLUSION: ...]` — memo scaffold marker where the conclusion is blank by design.
- `[FACT NEEDED: ...]` — draft scaffold marker where a required fact is missing from case notes. Staff gets the fact; no guessing.
- `[FUNDING RESTRICTION: ...]` — flag where the contemplated action may bump up against LSC, HUD, or other funder rules that the office's compliance person should weigh in on.
- `CHECK WITH [MANAGING ATTORNEY] BEFORE SENDING` / `BEFORE FILING` — supervision-flag label applied in "configurable flags" supervision mode to outputs on flagged topics.

Trust the flags more than the absence of flags. An unflagged statement means the skill is confident — it does not mean staff or the attorney skips verification. ABA Formal Opinion 512 requires verification regardless.

## Supervision workflow (configurable)

Whether the plugin includes a formal review workflow — staff draft → managing attorney review → approved — is a genuine open question. Some offices want a hard gate on everything client-facing; others reserve it for filings and substantive advice letters.

The cold-start interview asks the managing attorney to choose:

1. **Formal review queue** — client/court-bound output queues, managing attorney approves, all logged
2. **Configurable flags, informal review** — certain triggers label output "CHECK WITH MANAGING ATTORNEY," no queue mechanism
3. **Lighter-touch** — standard safeguard labels on everything, managing attorney supervises through existing office structure (case rounds, one-on-ones, file reviews)

Changeable later by editing `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md`.

## Rolling staff turnover: the `/onboard` and `/case-transfer` solution

Unlike clinics, legal aid offices don't reset every semester — but staff transitions are constant. A paralegal leaves, an attorney goes on parental leave, a staffer rotates from intake to housing. `/onboard` is the interactive new-staffer flow — it reads the office handbook the managing attorney uploaded at setup and teaches it, with low-stakes practice exercises before the staffer touches a real case.

`/case-transfer` produces a per-case handoff memo: status snapshot, what's pending, what's next, deadlines, conflict re-check for the incoming attorney, open questions for the supervisor.

## Framework: ABA Formal Opinion 512 (2024) and LSC program rules

The ethical framework this plugin operates within. Lawyers may use generative AI but must ensure competence in the technology, maintain confidentiality, supervise outputs, communicate with clients about AI use where appropriate, and verify before relying. LSC-funded programs operate additionally under 45 CFR Part 1600. The safeguards above — labels, confidence indicators, verification prompts, the explicit non-authority of research outputs, the `[FUNDING RESTRICTION: …]` flag — are built for this model.

The plugin is designed to operate the way an experienced legal aid managing attorney would want it to.

## Skills

| Skill | Purpose |
|---|---|
| **cold-start-interview** | Managing attorney's one-time setup — funding sources, practice areas, eligibility, jurisdictions, supervision style, seed docs |
| **build-guide** | Managing attorney's per-practice-area guide — intake, escalation posture, review gates, cross-practice checks |
| **onboard** | New staff onboarding — procedures, tools, jurisdiction basics, practice exercises |
| **eligibility-screening** | Funder-aware screening — not just LSC. Income, assets, residency, § 1626 status, conflicts, case-type priority. Produces a funding-source matrix so a matter that fails LSC but qualifies under IOLTA gets routed correctly |
| **client-intake** | Practice-area-specific intake with cross-area issue spotting, urgency triage, and Rule 1.18 prospective-client confidentiality baked in from the first question |
| **draft** | Eviction answers, protective orders, demand letters, benefits appeals, asylum apps. Jurisdiction-aware. Every draft labeled as a draft. Every cite tagged for verification |
| **memo** | IRAC scaffolding with research gaps flagged. Rule blocks are RESEARCH NEEDED; Application is STAFF ANALYSIS prompts; Conclusion is blank |
| **research-start** | Research roadmap — leads, not authoritative citations. Statutory candidates, case-law areas, agency guidance, search terms |
| **status** | Audience-aware case summaries — client (6th-grade plain language), internal (managing attorney), court-ready, funder |
| **client-letter** | Routine correspondence from templates — appointment confirms, doc requests, brief updates, engagement letters, decline letters, closing letters |
| **managing-attorney-review** | Three supervision models. Formal queue, configurable flags, or lighter-touch. The plugin adapts to how the office actually supervises, not the other way around |
| **deadlines** | Track case deadlines — add, cross-case rollup, warning cadence (14/7/3/1 days), overdue flags. Funder-tagged for capacity planning |
| **client-comms-log** | Append-only per-case communication record. Rule 1.18-aware for prospective clients; VAWA-confidentiality-aware for DV matters |
| **case-transfer** | For staff turnover. Smooth transitions, leave coverage, role changes, intake-to-staff handoffs. The 60-second narrative the new attorney actually reads |
| **lsc-report** | LSC case service reporting, IOLTA narratives, HUD reports, foundation milestones. Extracts what each funder requires from your case-management data |

## Connectors and citation verification

**Connect a research tool first — the citation guardrails depend on it.** Without one, every cite is tagged `[verify]` and the reviewer note above each deliverable records that sources weren't verified.

The legal research connectors in this plugin aren't just data sources — they're the difference between a verified citation and one you have to check. **CourtListener** (Free Law Project) and **Descrybe** handle primary law verification. **Courtroom5** provides jurisdiction-aware procedural guidance for pro se referrals.

Provider-specific public-facing intake integrations — including LawDroid's Legal Information Assistant (LIA) and Voice Intake Assistant (VIA), which feed upstream caller conversations into the plugin's eligibility-screening and client-intake skills via MCP — are **not bundled** with this community plugin. They are available as part of LawDroid's custom-build engagement — see *Custom builds* below.

## Integrations (open questions)

Ships with the connectors in `.mcp.json`. Office-specific case management systems (Legal Server, LegalFiles, Clio, PIKA, Pro Bono Net, JusticeServer) are noted as priority future integrations — most civil legal aid offices use one of these. Until a connector exists, the plugin reads case data from file upload or via the office's own integration.

Account tier for client confidentiality is an open question for each office's IT and compliance review. The plugin's Part 0 ethical preconditions ask this directly.

## How it learns

Your practice profile at `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` isn't static — it improves as you use the plugin. Skills tell you when an output used a default you should tune.

## File structure

```
legal-aid/
├── .claude-plugin/plugin.json
├── .mcp.json
├── CLAUDE.md                           # Managing attorney's office config — written by cold-start
├── README.md
├── deadlines.yaml                      # operational deadline ledger
├── skills/
│   ├── cold-start-interview/
│   ├── build-guide/
│   ├── onboard/
│   ├── eligibility-screening/          # NEW — funding-source-aware screening
│   ├── client-intake/
│   ├── draft/
│   ├── memo/
│   ├── research-start/
│   ├── status/
│   ├── client-letter/
│   ├── managing-attorney-review/
│   ├── deadlines/
│   ├── client-comms-log/
│   ├── case-transfer/                  # NEW — rolling staff transitions
│   └── lsc-report/                     # NEW — funder reporting data extraction
├── case-transfers/                     # per-transition handoff memos
│   └── [YYYY-MM]/
│       ├── _summary.md
│       └── [case-id].md
└── client-comms/                       # per-case communication logs
    └── [case-id]/
        └── log.md
```

## About this plugin

`legal-aid` is an open-source community plugin published by [LawDroid Ltd.](https://lawdroid.ai), licensed under Apache 2.0. It is not affiliated with Anthropic and is not part of the `anthropics/claude-for-legal` official suite. It is designed to interoperate cleanly with that suite — particularly with `legal-clinic` — for offices that run both.

LawDroid built this plugin because Anthropic's official Claude for Legal launch (May 2026) included a `legal-clinic` plugin for law school clinics, but did not ship anything for the much larger civil legal aid and court self-help sector. This plugin fills that gap.

We are publishing it openly so any legal aid organization, court self-help program, or public-interest legal services provider can deploy it for free. The plugin is fully self-contained — no LawDroid services or accounts are required to use it. Issues, pull requests, and forks are welcome at the GitHub repository.

## How this plugin differs from `legal-clinic`

`legal-clinic` (Anthropic-authored, in the official `anthropics/claude-for-legal` repository) covers law school clinics — supervising professors, student practice rule authority, semester onboarding, pedagogy posture. This plugin covers the much larger civil legal aid sector — staff attorneys and paralegals, rolling staff turnover, LSC and IOLTA reporting obligations, intake volume in the thousands per office per year, and eligibility screening as a first-class workflow rather than an afterthought.

The two plugins share architectural DNA — both write to a per-plugin `CLAUDE.md` practice profile, both share `company-profile.md` one level up if used together, both use the same confidence-marker vocabulary — so an organization that runs both (a legal aid org with a law school clinical partnership, or a hybrid externship program) gets consistent behavior across them.

## Installation

The plugin lives in this repository and installs as a Claude plugin. From a working Claude Code or Claude Cowork environment:

1. Clone or download this repository.
2. Point Claude at the plugin directory:
   ```
   claude plugins install ./legal-aid-plugin
   ```
   (Or, in Cowork: Settings → Plugins → Add local plugin → select the directory.)
3. Run the cold-start interview:
   ```
   /legal-aid:cold-start-interview
   ```
4. Have staff onboard with:
   ```
   /legal-aid:onboard
   ```

The plugin works without any connectors using local file access. Connecting CourtListener, Descrybe, Courtroom5, Slack, and Google Drive improves output quality but is not required.

## Documentation

Complete plugin documentation is available here: [Full Documentation](https://legalaidplugin.org/docs)

---

## Custom builds

The community plugin ships with the core skills and a generic configuration template. **LawDroid offers custom-build engagements** for organizations that need:

- **Per-org deployment** — configuration, training, IT/compliance integration, managing-attorney coaching, and funder-specific reporting beyond the generic `/lsc-report` scaffold.
- **Per-jurisdiction playbooks** — state-specific eligibility tables, pleading templates, court self-help protocols, and local-rule supplements. Drop into the practice-area guides system the plugin already exposes.
- **Public-facing intake** — integrate LawDroid's Legal Information Assistant (LIA) and Voice Intake Assistant (VIA) to provide community members with reliable legal information and vetted intake triage. Upstream caller conversations feed into the plugin's eligibility-screening and client-intake skills via MCP so callers don't repeat their story when their matter reaches a staffer.

Contact LawDroid for engagement scoping. Custom builds preserve the open-source core — any office using the community plugin is fully supported by the plugin's own skills.

## Contributing

Issues, suggestions, and pull requests are welcome at the GitHub repository.

- **Bug reports**: please include the SKILL.md and the prompt that produced the unexpected behavior.
- **New skills**: open an issue first to discuss scope and naming before opening a PR.
- **Jurisdiction-specific contributions**: improvements to skills' generic per-state defaults (formatting, common case types, agency naming) are welcome. State-specific plausibility ranges for `/deadlines` are captured per-office at cold-start rather than in a shared reference file, so contributions there go in the office's own CLAUDE.md.
- **Translations**: skill output and CLAUDE.md template translations for languages the legal aid sector commonly serves (Spanish, Vietnamese, Mandarin, Haitian Creole, Arabic) are welcome.

## License and credits

Apache License 2.0. Architectural pattern (CLAUDE.md template, SKILL.md frontmatter, .mcp.json schema) studied from Anthropic's `legal-clinic` plugin in the `anthropics/claude-for-legal` repository, and refactored for civil legal aid practice. All civil-legal-aid-specific design — supervision model, eligibility-screening skill, funder-restriction flag system, case-transfer pattern, LSC reporting — is original to this plugin.
