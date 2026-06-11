---
name: spec-status
description: 'Update task statuses in the Implementation Tasks table of a specification and keep the aggregate spec status in .specs/plan.yaml in sync as implementation progresses. Use when starting, completing, blocking, unblocking, or reopening a spec task while implementing.'
---

# Spec Status

Use this skill while implementing a specification to keep progress truthful in two places at once:

1. The **Implementation Tasks** table (section 12) of the spec file — the per-task status.
2. The **aggregate `status`** of that spec in `.specs/plan.yaml` — derived from the table.

This is the in-the-loop companion to the other two spec skills, and it owns neither of their formats:

- **`specs`** defines the Implementation Tasks table and the allowed task statuses; use it to *create* the spec, *add* tasks, or change the spec's content.
- **`spec-plan`** defines the `.specs/plan.yaml` schema and the status-aggregation rule; use it to *(re)build* the plan, validate it, or report what is unblocked.
- **`spec-status`** (this skill) only *flips statuses* during implementation and keeps the two files consistent. Do not use it to add or remove tasks, edit task descriptions beyond a blocking reason, or restructure the plan — defer those to the skills above.

## When to Use

- You started working on a task and it must move off `pending`.
- A task is finished and verified, or is blocked, unblocked, or must be reopened.
- A task status changed and the spec's aggregate `status` in `.specs/plan.yaml` is now stale.

Reach for this skill after each meaningful transition, not in a batch at the end — the table and plan should reflect reality at any point an interruption could occur.

## Task Statuses

The Status cell of each row in section 12 must hold exactly one bare keyword so `spec-plan` can parse it:

| Status        | Meaning                                                                 |
|---------------|-------------------------------------------------------------------------|
| `pending`     | Not started.                                                            |
| `in-progress` | Actively being implemented.                                             |
| `completed`   | Implemented and verified — its requirements, acceptance criteria, and tests pass. |
| `blocked`     | Cannot proceed; the reason is recorded (see below).                     |

### Allowed transitions

- `pending` → `in-progress` — when you begin the task.
- `in-progress` → `completed` — only after the task's Related Requirements and the relevant Acceptance Criteria (section 5) are satisfied and verified. Never mark `completed` on the basis of code written but unverified.
- `pending` | `in-progress` → `blocked` — when something prevents progress.
- `blocked` → `in-progress` | `pending` — when the blocker is resolved.
- `completed` → `in-progress` — when verified-done work must be reopened for rework.

### Recording a blocking reason

Keep the Status cell to the bare keyword `blocked` so aggregation stays machine-readable, and append the reason to that row's **Task** cell, e.g. `— blocked: waiting on schema-user-account TASK-003`. Remove the appended note when the task is unblocked.

## Procedure

1. **Locate the spec.** Find the spec file under `.specs/` and confirm it has a section 12 Implementation Tasks table. If the task you are working on is not a row in that table, stop and use the `specs` skill to add it first — this skill does not create tasks.
2. **Apply the task transition.** Edit only the Status cell of the affected row(s) following *Allowed transitions*. Change nothing else in the table except a blocking reason in the Task cell. Preserve column alignment and the rest of the file.
3. **Re-derive the aggregate status** for that spec using `spec-plan`'s rule, applied to the table after your edit:
   > `blocked` if any task is blocked, else `in-progress` if any task is in-progress, else `completed` if tasks exist and all are completed, else `pending`.
4. **Sync `.specs/plan.yaml`.** If the file exists, update that spec's `specs[].status` to the value from step 3, and set the top-level `generated` field to today's date (`YYYY-MM-DD`) so the plan is not flagged stale by `spec-plan`'s Validate mode. Touch nothing else — leave `order`, `cycles`, `depends_on`, and every other spec's entry unchanged. If `.specs/plan.yaml` does not exist, note that the plan has not been generated and offer to run `spec-plan`.
5. **Keep edits minimal and deterministic.** A status transition should produce a small, reviewable diff: the changed Status cell(s), an optional blocking note, one `specs[].status` value, and the `generated` date.

## After a Transition

- If you just marked a spec's last task `completed` (aggregate became `completed`), other specs may now be unblocked. Offer to run `spec-plan` in **Next** mode to report what is ready to start.
- If the change introduced or removed tasks, renamed a spec, or otherwise altered structure rather than just status, that is out of scope here — hand off to `specs` (table/content) or `spec-plan` (plan rebuild) instead of editing `plan.yaml` by hand.

## Example

A spec `.specs/tool-auth-login.md` with section 12:

| ID       | Task                       | Related Requirements | Status      |
|----------|----------------------------|----------------------|-------------|
| TASK-001 | Define login request DTO   | REQ-001              | completed   |
| TASK-002 | Implement login handler    | REQ-002, SEC-001     | in-progress |
| TASK-003 | Add rate limiting          | SEC-002              | pending     |

Starting TASK-003 flips its Status to `in-progress`. The aggregate is still `in-progress` (TASK-002 and TASK-003 in progress, TASK-001 completed), so the spec's `status` in `.specs/plan.yaml` stays `in-progress` and only `generated` is bumped. Once every row reads `completed`, the aggregate becomes `completed` and `plan.yaml` is updated to match.
