---
name: onboard
description: >
  New-staff onboarding — office procedures, tools walkthrough, jurisdiction
  basics, supervision model, funder restrictions, and practice exercises
  before real cases. Reads the procedures manual the managing attorney
  uploaded at setup and teaches it interactively. Use when a new staffer
  says "onboard me", "I'm new to the office", "getting started", or when
  a paralegal, staff attorney, intake specialist, or volunteer attorney
  joins. Pass --card for the one-page reference.
argument-hint: "[role: paralegal|staff-attorney|intake|volunteer] [--card]"
---

# /onboard

1. Check `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` is set up. If placeholders: "Ask [managing attorney] to run `/legal-aid:cold-start-interview` first — every command depends on it."
2. Identify the staffer's role and adjust the walkthrough.
3. Walk through: office context (from procedures manual) → supervision model → funder restrictions → commands → practice exercises (eligibility screen, intake, draft) → verification habits.
4. `--card`: generate the one-page reference card.

```
/legal-aid:onboard
```

```
/legal-aid:onboard --card
```

---

# Onboard: New-Staff Orientation

## Purpose

Legal aid offices don't reset every semester, but staff transitions are constant — a paralegal leaves, a staff attorney joins, an intake specialist rotates from housing to family, a volunteer attorney comes in for a six-month rotation. Each transition is a malpractice exposure point until the new staffer knows the office: how this office runs intake, how it screens for eligibility, what its funders restrict, how the managing attorney reviews work, what the local courts expect.

This skill is the guided walkthrough. It reads what the managing attorney uploaded during `/cold-start-interview` — the procedures manual, filing guides, eligibility tables, intake forms, conflict-check SOP — and teaches it interactively, with practice exercises so the staffer tries the tools in a low-stakes setting before a real prospective client is on the line.

**Audience: new staff** — paralegals, staff attorneys, intake specialists, volunteer attorneys. Managing attorneys don't run this (they run `/cold-start-interview`).

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → office profile, practice areas, jurisdiction, procedures manual path, supervision style, eligibility guidelines, funder restrictions, conflict-check process, practice-area templates.

If that file is missing or still contains placeholders: "The office hasn't been set up yet. Ask the managing attorney to run `/cold-start-interview` first — every other command in this plugin depends on it. You can come back here once setup is complete."

## Identify the role

Before starting, ask:

> What's your role here? (Paralegal / staff attorney / intake specialist / volunteer attorney / other.) The walkthrough adapts — intake specialists spend more time on eligibility screening and intake; staff attorneys spend more time on drafting and memos; paralegals usually do everything depending on the office.

Record the answer. The walkthrough emphasizes different commands and exercises based on role, but the verification habits and supervision model are the same for everyone.

## The walkthrough

### Opening

> Welcome to [office name]. I'm going to walk you through how this office works and how to use these tools — about twenty to twenty-five minutes, and you can pause anytime. By the end you'll have run a practice eligibility screen, a practice intake, drafted a practice document, and you'll know what the office expects before you touch a real prospective client.
>
> One thing up front: everything I produce is a starting point, not final work. You and the managing attorney do the lawyering. I handle the formatting, the structured screening, the first draft, and the deadline tracking so your time goes to the matters and the analysis, not to writing "Dear Sir or Madam" for the twentieth time.

### Part 1: This office (5 min)

Read from `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` and the ingested procedures manual. Cover interactively:

- **Practice areas** — what the office handles, what it doesn't, and where to refer callers with out-of-scope matters (court self-help, lawyer-of-the-day, private bar referral)
- **Clients** — who they are, what they're facing, languages spoken, common referral sources
- **Jurisdiction** — counties served, primary courts, local rules and standing orders the office tracks, any specialty courts (housing, family, immigration, administrative)
- **Funding sources** — which funders apply (LSC / IOLTA / HUD / VAWA / state / foundation), and what each one restricts. Be specific: "If you're working on a case under LSC funds, 45 CFR Part 1600 says you can't [class actions / fee-generating / certain immigration matters / lobbying / etc.]. If the matter would fail those restrictions but the client is otherwise eligible, the office runs it under [other funding source] instead."
- **Supervision style** — how the office reviews work before it goes out (per the model in CLAUDE.md). Be very specific: "Before [filings / advice letters / anything client-facing] goes out, [it queues in the formal review system / you flag it with 'CHECK WITH MANAGING ATTORNEY' and ping them / you take it to case rounds Tuesday morning]."
- **Conflict-check procedure** — what the office runs, who runs it, when. Walk through one example.

Don't lecture — check understanding. Ask: "So if a caller comes in with an eviction and also mentions they're undocumented, what's the call here?" (Answer depends on the office — usually: screen both issues; LSC § 1626 status matters for the housing matter; non-LSC funds may serve the immigration question; both go to managing-attorney review if there's any uncertainty.)

### Part 2: Supervision and Rule 1.18

Five-minute interactive segment specifically on supervision and prospective-client confidentiality:

> Two things that bite legal aid offices that I want you to internalize before you do anything else:
>
> **One: every prospective client triggers Rule 1.18.** When a caller starts telling you their situation — even before the office accepts the case, even if the office never accepts the case — duties of confidentiality and limited duties of loyalty attach. That means: information from a screening or intake is privileged-adjacent; you don't share it casually; conflicts with the prospective client matter even on declined matters. The eligibility-screening output marks this explicitly, but the habit needs to be yours, not the tool's.
>
> **Two: nothing goes to a client or a court without the supervision model running.** In this office, that means [restate the supervision model from CLAUDE.md]. The plugin labels every output as a draft, but the gating is on the human side. If you're about to send something and you can't remember whether it cleared the model, stop and check.

### Part 3: The commands (5 min)

Walk through each command the staffer will actually use. Emphasize different commands by role:

| Command | When you use it | What you get |
|---|---|---|
| `/eligibility-screening` | First call from a prospective client | Funder-source matrix, conflict flags, priority classification, referral suggestion if ineligible |
| `/client-intake` | After eligibility clears | Practice-area-specific case summary, cross-area issue spotting, urgency triage |
| `/draft [doc]` | Need a first draft | Practice-area template filled from case notes — *starting point, not final* |
| `/memo` | Internal case analysis | IRAC-format scaffold with research gaps flagged |
| `/research-start [issue]` | Starting legal research | Roadmap: statutes, case law areas, agency guidance, search terms — *leads, not authorities* |
| `/status [audience]` | Updating someone on a case | Summary tailored to client / internal / court / funder |
| `/client-letter [type]` | Routine correspondence | Appointment confirm, doc request, status update, referral letter from templates |
| `/deadlines` | Tracking case deadlines | Add, cross-case rollup with 14/7/3/1-day warnings, overdue flags |
| `/case-transfer` | Moving a case to another staffer | Per-case handoff memo |
| `/client-comms-log` | Logging a call/email | Append-only per-case communication record |

For each command: what it does, what it explicitly doesn't do, what *you* verify before relying on it.

If the office has the LawDroid LIA-MCP or VIA-MCP connectors enabled (check the integrations table in CLAUDE.md), mention:

> If a caller spoke with LIA before this matter landed on your desk, or called the office's intake line and went through VIA, the eligibility-screening and intake skills can pull that conversation directly. You won't ask the person to repeat what they already told us — you'll start from where they left off and ask only the follow-ups needed to complete the screen.

### Part 4: Practice exercises (8-10 min)

**Low-stakes. Fake prospective client. Real tools.**

**Exercise 1 — Practice eligibility screen:**
> Here's a fake caller scenario: [practice-area-appropriate hypothetical — e.g., for a housing-focused office, "Marisol, single mother of two, just received a 5-day notice to quit. Works part-time at $14/hour, takes home about $1,700/month. Lives in Greensboro. Her landlord is a regional management company — RealCo Properties. She mentions her cousin used to work for RealCo. She's a U-visa applicant."]
>
> Run `/eligibility-screening` and screen me as if I'm Marisol. I'll answer as she would. At the end, look at the funder-source matrix — what does the screen recommend? Did it spot the U-visa § 1626 exception? Did it flag the conflict-check on RealCo? Where does it route?

Debrief: what the screen caught, what *you* should have probed deeper on, what the office's managing attorney will want to see before approving acceptance.

**Exercise 2 — Practice intake:**
> Assuming the office accepts Marisol's case under [funding source], run `/client-intake`. The screening output should propagate — you shouldn't be re-asking her income or about the U-visa.
>
> What did the intake spot beyond the housing issue? Did it flag the U-visa as a possible cross-area matter requiring referral or supplementation? Did it flag the funder restriction propagated from screening?

The point: intake builds on screening, doesn't restart it.

**Exercise 3 — Practice draft:**
> Using Marisol's intake, run `/draft eviction-answer`. You'll get a first draft.
>
> Read it. What's right? What's wrong? What would you change before showing it to [managing attorney]? Did it flag any [FUNDING RESTRICTION] concerns from the funder source she was accepted under? Did it use the local caption format the office uploaded, or default to state form?

The point: the draft is competent, not final. The staffer learns to read critically.

**Exercise 4 — Research roadmap (staff attorney / paralegal only):**
> Run `/research-start "habitability defense to eviction in [state]"`. You'll get a roadmap — statutes, case law areas, agency guidance, search terms.
>
> None of those citations are verified. That's on purpose. Pick one statute and tell me how you'd verify it's current.

### Part 5: Verification habits (2 min)

The habits that matter most in legal aid practice:

- **Every output is a starting point.** If it went to a client or a court without you reading it critically, something failed.
- **Verify every citation.** `/research-start` gives leads, not authorities. Use CourtListener or Descrybe before relying on a cite.
- **Funder restrictions propagate, but don't enforce themselves.** A `[FUNDING RESTRICTION: ...]` flag means stop and check — usually with the managing attorney, sometimes with the office's compliance lead. Don't strip flags to make things tidier.
- **Rule 1.18 applies to declined matters too.** A caller who screens out is still entitled to confidentiality on what they told you. The screening output stays in the privileged file store, not the regular one.
- **When uncertain, the output says so.** A `[UNCERTAIN: ...]` or `[VERIFY: ...]` flag is a prompt to investigate or ask the managing attorney, not to delete the flag.
- **Check current eligibility tables.** Income thresholds change annually; the plugin doesn't know the current FPL table. Verify against the office's posted chart.
- **[Supervision reminder per CLAUDE.md model]** — what gets reviewed before it goes out, and how.

### Closing

> That's it. You've run an eligibility screen, a structured intake, a first draft, and a research roadmap. Your first real prospective client will feel similar, except the person is real and the managing attorney is reviewing your work.
>
> The one-page reference card: `/legal-aid:onboard --card`. Print it, pin it. The first week or two, you'll glance at it more than you expect.

## `/legal-aid:onboard --card`

Generate a one-page reference card. Contents:

- **The commands** (table from Part 3, condensed)
- **What the plugin produces / does not produce** (starting points yes, final work product no, authoritative citations no, acceptance decisions no, funder rules enforcement no)
- **Verification habits** (the bullets from Part 5)
- **Supervision model in one line** (per CLAUDE.md)
- **Funder-restriction flag short-form** (per CLAUDE.md funders)
- **Rule 1.18 reminder** (one sentence)
- **Who to ask when stuck** (managing attorney name from CLAUDE.md)

Printable. One page. Hand it out on day one.

## Cross-skill notes

- `/onboard` does not write to `CLAUDE.md`. Only `/cold-start-interview` and `/build-guide` do.
- If the office adds a new funder, practice area, or supervision change after a staffer is onboarded, that staffer should run `/onboard --card` again to get the updated reference card. The walkthrough itself can be re-run with no penalty.
- For volunteer attorneys on short rotations, consider an abbreviated version: Part 1 + Part 2 + the commands table + one practice exercise + the card. Skip the deeper practice exercises if the volunteer is only handling a narrow matter.

## What this skill does NOT do

- Replace the managing attorney's orientation. It covers procedures and tools; the managing attorney covers judgment, the office's case-handling judgment calls, the human relationships, and the things you only learn by watching someone good do the work.
- Teach substantive law. Practice-area *orientation*, not a doctrinal course.
- Certify the staffer as ready. The managing attorney decides when a staffer takes a real prospective client.
- Run a real eligibility screen or real intake. The practice exercises use fake scenarios; real callers get the real workflow, with the real conflict check and the real supervision gates.
