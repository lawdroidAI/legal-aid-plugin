---
name: managing-attorney-review
description: >
  Managing attorney's review queue — staff output waits here for approval
  before going to clients or courts. Only active if "formal review queue"
  supervision style was chosen at setup; dormant under "configurable flags"
  or "lighter-touch" modes. Use when the managing attorney wants to see
  what's waiting for review, approve, edit-then-approve, or return an item.
  Emergency-tier items surface at top regardless of queue position.
argument-hint: "[--approve ID | --return ID 'note' | --edit ID | --acknowledge-flag ID]"
---

# /managing-attorney-review

1. Check `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → supervision style. If NOT "formal review queue": explain the office is set up for [configurable flags / lighter-touch], no formal queue exists, and how to switch.
2. Use the workflow below.
3. Default: show what's waiting — Emergency items first, then by funder-restriction status, then by due date, then by staffer.
4. Actions: approve / edit-then-approve / return with note / acknowledge live funder-restriction flag. All logged.

```
/legal-aid:managing-attorney-review
/legal-aid:managing-attorney-review --approve Q-003
/legal-aid:managing-attorney-review --return Q-004 "Check the local service requirement — Wake County changed last month"
/legal-aid:managing-attorney-review --acknowledge-flag Q-007
```

---

# Managing-Attorney Review Queue (Conditional)

## Purpose

When the office runs formal-queue supervision, staff output that's client-facing or court-bound queues here for managing-attorney approval before going out. This skill is the queue's interface — what's waiting, in what order, with what flags, and the actions to clear items.

The skill is conditional. Under "configurable flags" mode, output carries `CHECK WITH MANAGING ATTORNEY` labels but doesn't queue formally — the managing attorney reviews via the office's existing structure (case rounds, one-on-ones, hallway). Under "lighter-touch" mode, output carries only the standard verification labels — no review-specific gating. In both of those modes, this skill is dormant.

## Mode check

Read `~/.claude/plugins/config/claude-for-legal/legal-aid/CLAUDE.md` → `## Supervision style` → Model field.

- **"formal review queue"** → proceed
- **"configurable flags, informal review"** → respond: "Your office uses configurable flags, not a formal queue. Staff output is labeled with 'CHECK WITH MANAGING ATTORNEY' triggers but doesn't queue here. To switch to formal queue, run `/legal-aid:cold-start-interview --redo supervision`. To see what's currently flagged across the office, ask the staffers directly or check case rounds notes."
- **"lighter-touch"** → respond: "Your office uses the lighter-touch supervision model. No queue exists. Staff output carries the standard AI-assisted label and verification prompts; review happens through case rounds, one-on-ones, and file reviews. To enable a formal queue, run `/legal-aid:cold-start-interview --redo supervision`."

## Queue structure

Items in the queue carry metadata:
- Item ID
- Submitting staffer
- Case ID + practice area
- Output type (draft / memo / client-letter / status / engagement-letter / decline-letter / closing-letter / other)
- Submitted timestamp
- Urgency tier (Emergency / Time-sensitive / Standard — from intake or from the submitting skill)
- Live funder-restriction flags (propagated from intake and/or net-new from drafting)
- Live conflict flags (from intake — should be resolved before queue submission, but listed if present)
- Output content (the drafted text)

## Default view: the queue

When invoked without arguments, show:

```
# Review Queue — [office name] — [date]

[Emergency tier — if any]
-----
[Q-XXX] [Staffer] · [Case ID] · [Output type]
        Urgency: EMERGENCY · Submitted [time]
        [Funder restrictions if any: list]
        [Preview — first 2 sentences]
        Actions: --approve | --edit | --return | --acknowledge-flag

[Time-sensitive — if any]
-----
[same structure]

[Standard — if any]
-----
[same structure]
```

Sort order:
1. Emergency tier always first, regardless of submission time
2. Items with unacknowledged funder-restriction flags next within each tier
3. Items with unresolved conflict flags next — flag the conflict directly and recommend rejection until resolved
4. Then by submission time (oldest first)

At the top of the queue display, summary statistics:
- Emergency count
- Total items in queue
- Oldest pending item (with submitter — sometimes the same staffer's items are stuck)
- Items with funder-restriction flags
- Items with conflict flags

## Actions

### `--approve [ID]`

Mark the item approved. Logged with:
- Reviewer name (from CLAUDE.md)
- Approval timestamp
- Reviewer note (optional)

After approval, the output is released to the staffer's outbox with a header note: "Approved by [managing attorney] on [date]. You can send / file."

If the item has a live `[FUNDING RESTRICTION: ...]` flag and the managing attorney is approving anyway, prompt: "This item has a live funder-restriction flag. Approval implies you have decided the action is allowed under the funder's rules. Confirm acknowledgement, or use `--acknowledge-flag` first to resolve the flag, or `--return` to send back for revision."

If the item has an unresolved conflict flag: refuse to approve. "Item Q-XXX has an unresolved conflict flag from intake. Resolve the conflict first (waiver in writing, declination, or fact-clarification), then re-approve."

### `--return [ID] "note"`

Return the item to the staffer with a note. Logged with:
- Returner name
- Return timestamp
- Note (required — empty note returns are not allowed)

The staffer receives the item back with the note prominent at the top, and can revise and resubmit.

### `--edit [ID]`

Open the item for editing inline. After edits, the managing attorney can `--approve` (releases the edited version with the staffer notified of the edits) or `--return` (sends the edits back as suggestions, staffer integrates).

### `--acknowledge-flag [ID]`

Specifically address a live funder-restriction flag on the item. Prompts:

> Item Q-XXX has flag: `[FUNDING RESTRICTION: <restriction>]`. How is this resolved?
> 1. The restriction does not apply on these facts (explain)
> 2. The matter is being moved to non-restricted funding (which funding source)
> 3. The action is being modified to avoid the restriction
> 4. The matter is being declined / withdrawn
> 5. Other (explain)

Records the resolution and clears the flag. Logged. The flag remains in the matter's history as resolved-with-explanation; future skills working on this matter see the resolution.

## Special handling: Emergency items

Emergency-tier items (set by `/eligibility-screening` or `/client-intake` based on urgency triggers like imminent lockout, hearing this week, deportation imminent, safety threat):

1. Always sort to top of queue regardless of submission time
2. Skill output (when the queue is invoked) starts with an "EMERGENCY ITEMS AWAITING REVIEW" header banner if any exist
3. Items in the queue for more than 4 hours under Emergency tier trigger a separate prompt: "Item Q-XXX has been in the Emergency queue for [duration]. Civil legal aid emergencies often don't accommodate review windows. Either approve, return, or escalate (which assigns to a different managing attorney if multiple are configured)."

The skill does not skip review for emergencies. But it surfaces them aggressively.

## Output

Per default view above. After actions, a short confirmation:

```
✓ Item Q-XXX [action]. [Resulting state.]
Logged at [timestamp] under [reviewer name].

[Next queue summary: N items remaining, X Emergency, Y with funder flags]
```

## Queue storage

Queue state is persisted at `~/.claude/plugins/config/claude-for-legal/legal-aid/review-queue/` with one file per item. Action history (approvals, returns, edits, flag acknowledgements) is in `~/.claude/plugins/config/claude-for-legal/legal-aid/review-queue/_history.yaml`.

The directory and the history file are part of the office's privileged file store. Treat accordingly.

## Cross-skill propagation

- Items approved here propagate the approval into the originating skill's case-file entry. A drafted answer that's approved becomes filing-ready in the matter's case file.
- Items returned here propagate the note back to the staffer's view of their pending work.
- Funder-restriction flag resolutions logged here propagate to the matter's flag history; downstream skills see the resolution and don't re-raise the same flag.
- Conflict flags that block approval here surface in the matter's case file and prompt the staffer to drive resolution.

## What this skill does NOT do

- Run under non-formal-queue supervision modes. Dormant under "configurable flags" and "lighter-touch."
- Approve items with unresolved conflict flags. Refuses; conflict resolution is a prerequisite.
- Edit the originating skill's output template. Edits apply to the item at review, not back to the underlying skill.
- Send approved items. Approval releases to the staffer's outbox; staffer sends per office workflow.
- Cover supervision that happens outside the queue. Case rounds, one-on-ones, hallway corrections continue as they always have; the queue is one mechanism among several.
- Auto-approve based on staffer experience. Every item, every staffer, every time. No reputation-based fast-path.
