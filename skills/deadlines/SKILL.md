---
name: deadlines
description: >
  Track case deadlines — add, cross-case rollup, update, complete, close.
  Warns at configurable thresholds (default 14/7/3/1 days); overdue items
  stay flagged until resolved. Funder-tagged so the rollup can filter by
  funder source for capacity planning. The operational record for a legal
  aid office's workload. Use when staff or managing attorney needs to add
  a deadline, ask what's due this week, get a deadline report, or update
  a case deadline.
argument-hint: "[--add | --report (default) | --update [id] | --complete [id] | --close [id] | --horizon=N | --by-funder=lsc|iolta|...]"
---

# /deadlines

1. Load `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → jurisdiction(s), practice areas, funder restrictions, warning-day cadence.
2. Use the workflow below.
3. Route by flag:
   - `--add`: capture case ID, type, description, due date, source, owner_staff, owner_attorney, funder_source. Write to `~/.claude/plugins/config/claude-for-legal/legal-aid/deadlines.yaml`. Check for duplicates.
   - `--report` (default): cross-case rollup — overdue, next 3d, next 7d, next 14d; by owner; by practice area; by funder; unassigned flags.
   - `--update [id]`: modify fields; log note with date.
   - `--complete [id]`: mark done; confirm with staffer that work is actually filed / submitted.
   - `--close [id]`: close-without-completing; require rationale in notes.
   - `--horizon=N`: limit report to next N days.
   - `--by-funder=X`: filter report to deadlines on cases allocated to funder X.
4. Confirm any write before committing.

```
/legal-aid:deadlines
/legal-aid:deadlines --add
/legal-aid:deadlines --report --horizon=30
/legal-aid:deadlines --report --by-funder=lsc
/legal-aid:deadlines --complete D-2026-0143
```

---

# Deadlines

## Purpose

A legal aid office's biggest operational risk is a missed deadline. Staff carry 40-80 active matters, intake volume runs 3-5× capacity, and deadlines that live only in individual staffers' heads get dropped at handoff, get forgotten during a busy week, get missed when someone unexpectedly goes on leave. This skill is the central operational record.

The managing attorney is on the hook for every missed deadline. The skill is calibrated to that stakes level — warnings fire early, overdue items stay visible until explicitly resolved, transfers (via `/case-transfer`) pull the deadline list forward to the incoming staffer.

## Load context

`~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → jurisdictions (so deadline calculation gets the right state's rules), practice areas, funders served (for the funder-tagged rollup), warning-day cadence (default 14/7/3/1 days, override per office).

`~/.claude/plugins/config/claude-for-legal/legal-aid/deadlines.yaml` → the ledger itself.

## Workflow

### `--add` — Capture a new deadline

Ask:
- Case ID
- Type: filing / hearing / statute-of-limitations / discovery / cure-period / response / notice / administrative-appeal / other
- Description (one-line, plain language)
- Due date (specific date — not "soon" or "next week")
- Time (if applicable, with timezone — default to jurisdiction's timezone from CLAUDE.md)
- Source (what triggered this deadline — court order, statute, agency notice, client document, prior staffer's note)
- Owner_staff (which staffer is responsible for the action)
- Owner_attorney (managing attorney or staff attorney with oversight)
- Funder_source (which funder this case is allocated under — for the rollup)
- Notes (optional)

Before committing:
- Search the ledger for a possible duplicate (same case, same type, dates within 7 days). If found, prompt: "Existing deadline D-XXX may be the same — review before adding."
- Confirm the due-date calculation if the staffer provided a triggering event rather than the deadline itself ("The notice was served on April 22; what's the response deadline?" → don't compute; ask the staffer to confirm against local rules, or run `/research-start` to scaffold the calculation question).

The skill **does not calculate deadlines from triggering events**. Civil legal aid deadlines are local-rule and statute-specific — different states count days differently, different courts handle weekends differently, different agencies have different appeal windows. The staffer or managing attorney does the math against the actual rule; the skill records the result. Tag every deadline with its source so a successor can verify the calculation.

The skill **does perform a plausibility check** if the office configured high-volume deadlines at cold-start. Plausibility checking is a guardrail against arithmetic errors and date-entry typos, not a substitute for the staffer's own computation.

#### Plausibility check (if configured)

Read `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → `## High-volume deadlines`.

If the table is populated and the deadline type the staffer is entering matches an entry (fuzzy match on type name plus triggering event), compute the days from the triggering-event date (if the staffer provided one in the source field or notes) to the entered due date. Compare to the typical-days-out range.

If outside the configured range, prompt:

> "The entered due date is **[N] days** after the triggering event. The office's practice profile says this deadline type is typically **[range] days** out, citing [source/authority]. This could be a legitimate variation (the rule allows it, local court grants extensions, the triggering-event date is uncertain), or it could be a calendar arithmetic error. Want to:
>
> 1. Recompute against the actual rule
> 2. Confirm and proceed (logged with override note)
> 3. Cancel this entry"

If the staffer chooses option 2, log the override with the reason they give. The deadline still gets added; the override is part of the audit trail.

If the entered deadline type doesn't match any entry in the plausibility table, proceed without checking — no false-positive on novel deadline types.

If the plausibility table is empty (office skipped this at cold-start), don't run the check at all. Don't prompt the staffer about it; the skill quietly adds the deadline as before.

Write to `deadlines.yaml` after confirmation. Assign an ID (sequential, with year prefix: D-2026-XXXX).

### `--report` (default) — Cross-case rollup

Group by:

**Overdue** (most prominent — red-flag visual treatment)
**Today** (today's items, time-sorted if times specified)
**Next 3 days**
**Next 7 days**
**Next 14 days**

For each item, show:
- ID
- Due date (with day of week)
- Case ID, client (if appropriate per CLAUDE.md privacy guidance)
- Type and short description
- Owner_staff
- Funder_source
- Source of deadline
- Any notes

Then aggregate views:

**By owner_staff** — who's carrying how many deadlines in the horizon
**By practice area** — workload distribution
**By funder** — useful for capacity planning ("we have 14 LSC deadlines and 3 IOLTA deadlines in the next 30 days")

**Unassigned flag** — any deadlines with owner_staff = null or "unassigned." These are the items most likely to drop. Surface prominently.

**Stale flag** — any deadline whose owner_staff has not been updated since the most recent `/case-transfer` for that case. Indicates the transfer may not have updated the deadline ownership.

### `--update [id]`

Modify any field. Always log:
- Who made the change
- When
- What changed (before → after)
- Reason (prompted)

### `--complete [id]`

Mark the deadline as completed. Before marking:

> Confirm completion: the filing / hearing / appeal / response was actually [filed / attended / submitted / completed]. Mark complete only after verification, not when the work is drafted or planned.

After confirmation, mark complete and log the completion date. The deadline drops out of the next report.

### `--close [id]`

Close without completing (e.g., case was withdrawn, deadline became moot, the matter took a different path). Require a rationale in notes. Logged with reason. Drops out of reports but remains in the ledger.

### `--horizon=N`

Limit the `--report` to next N days. Default horizon is 30 days. Useful for managing attorney's weekly look-ahead (`--horizon=7`) or quarter-end capacity planning (`--horizon=90`).

### `--by-funder=X`

Filter `--report` to deadlines on cases allocated to a specific funder. Particularly useful for capacity planning per funding source, or when responding to a funder's request for an update on workload distribution.

## Output

For reports, structured by tier:

```markdown
# Deadline Report — [office] — [date]

## Overdue (RESOLVE IMMEDIATELY)

[Each overdue item — most prominently displayed]

## Today
[Each item, time-sorted]

## Next 3 days
[Each item]

## Next 7 days
[Each item]

## Next 14 days
[Each item]

---

## By owner_staff

[Staffer]: N items in horizon
- [item summary]

## By practice area

[Area]: N items
[breakdown]

## By funder source

| Funder | N items in horizon |
|---|---|
| LSC | X |
| IOLTA | Y |
| ... | ... |

## Unassigned / stale flags

- [Each item with owner_staff = null or post-transfer stale]

## Premises this report rests on

- Ledger: `~/.claude/plugins/config/claude-for-legal/legal-aid/deadlines.yaml`
- Last modified: [timestamp]
- Items in ledger total: [N]
- Items not in this horizon: [N]

## What to verify

- Every overdue item: confirm whether actually overdue, or completed-but-not-marked-complete
- Every unassigned item: assign or close
- Every stale item: confirm with the case's current owner that ownership is correct
```

## Cross-skill propagation

- `/case-transfer` reads the deadlines.yaml file and surfaces this case's open deadlines in the transfer memo. The case-transfer memo offers to re-assign owner_staff for each deadline on the case.
- `/lsc-report` and other reporting skills may reference deadline volume in their narrative ("we handled N filings in the period" can be sourced from completed-and-filing-type deadlines).
- A deadline marked `[FUNDING RESTRICTION: ...]` in the case's intake/draft history is flagged at the deadline level too — if the deadline is on a matter with a live restriction flag, the report shows it next to the deadline.

## What this skill does NOT do

- Calculate deadlines from triggering events. Civil legal aid deadlines are local-rule-specific; the skill records, does not compute.
- Reassign owner_staff automatically on transfer. `/case-transfer` surfaces the deadlines; the transferring and receiving staffers (and the managing attorney) decide reassignment.
- Send reminders. The skill produces reports; the office's communication channels handle reminders (the report can be emailed or pasted in a Slack channel, but the skill itself doesn't push).
- Override a "completed" with "actually not completed." If a staffer marked something complete and then it turns out the filing didn't happen, the fix is `--update` with a note, not silent reversion. The history matters.
- Delete deadlines. Use `--close` with a rationale. The ledger is append-only in spirit; closed items remain visible to history.
