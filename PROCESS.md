> The canonical workflow for this delivery container. Read alongside `CLAUDE.md` (behaviour) and `SPEC.md`/`TASKS.md` (the actual work). Every step here is enforced — by you, by CI, or by the orchestrator's approval gates.

## 1. Branching

- **Base branch:** `main` on the upstream repo, unless `SPEC.md` explicitly names another (e.g. `develop`).
- **Delivery branch:** `delivery/$DELIVERY_ID` — already created by the container entrypoint. All commits land here.
- **Sub-task branches (optional):** `delivery/$DELIVERY_ID-<task-id>` for parallelisable or independently reviewable sub-tasks (hyphen separator, not slash — git refs cannot nest under the parent delivery branch). Merge back into the delivery branch via PR before the delivery PR closes.
- **Never:** commit to `main`, `master`, `develop`, or any branch outside `delivery/...`.

## 2. Commits

Conventional Commits, no exceptions:

```
<type>(<scope>): <subject>

<body — optional, hard-wrapped at 72>

Refs: $DELIVERY_ID
```

Types: `feat`, `fix`, `docs`, `style`, `refactor`, `perf`, `test`, `build`, `ci`, `chore`.

Subject in imperative mood, no trailing period, ≤ 72 chars. The body explains *why* — the diff already shows *what*. Always include the `Refs: $DELIVERY_ID` trailer so commits can be traced back to the orchestrator's records.

One logical change per commit. If you find yourself writing "and also" in a subject, split it.

## 3. Pull requests

- **Open early.** As soon as the first meaningful commit lands on the delivery branch, open the PR (draft if not ready for review). This gives CI a chance to run and the orchestrator a target to attach status updates to.
- **Title:** mirror the first commit subject.
- **Body template:**

  ```markdown
  ## Summary
  <2–4 sentences — what and why>

  ## Linked spec
  - SPEC: `SPEC.md` — section <X>
  - Tasks: <task-ids from TASKS.md>

  ## Changes
  - <bullet per logical change>

  ## Tests
  - Unit: <command + result>
  - Integration: <command + result, or "n/a">
  - Manual: <what was checked, or "n/a">

  ## Notes for review
  <anything reviewer attention should land on>

  Refs: $DELIVERY_ID
  ```

- **Draft → Ready:** flip out of draft only when CI is green and `Definition of Done` (§5) is complete.
- **Self-review first:** read your own diff before flipping to ready. Catch the obvious before the human does.
- **Reviewers:** the orchestrator assigns. Do not @-mention humans yourself.

## 4. Testing

Four layers, in order of speed. Earlier failures stop later layers.

| Layer | Where | When |
|---|---|---|
| Unit | Inside container, on save / before commit | Continuously during work |
| Integration | Inside container or CI sidecars | Before opening / un-drafting PR |
| End-to-end | CI only | On every PR push |
| Manual / approval | Human via orchestrator HITL | Before merge |

### 4.1 Unit

Run on every meaningful change. If the project has a watch mode, leave it running. Failures must be fixed before commit, not deferred.

### 4.2 Integration

Run before un-drafting the PR. If the project has integration tests gated behind a flag (slow, needs a DB, etc.), run them at least once before un-drafting and capture the output in the PR body.

### 4.3 End-to-end

Run by CI on PR push. Treat CI as the canonical signal — local pass with CI fail = fix-forward, not "works on my machine".

### 4.4 Manual / approval

The orchestrator routes manual approval prompts to Pete via the HITL flow described in `CLAUDE.md` §6. The PR is not merged until that approval lands.

## 5. Definition of Done

Before un-drafting a PR, every item on this list must be true. Tick them in the PR body.

- [ ] All `TASKS.md` items for the delivery are marked complete (or split out into a follow-up issue with rationale)
- [ ] CI is green: lint, types, unit, integration, e2e
- [ ] No new top-level dependencies introduced without a HITL-approved decision recorded in `artefacts/decisions/`
- [ ] No `TODO` / `FIXME` comments added by this delivery (existing ones may remain unless the task requires fixing them)
- [ ] Public API or behaviour changes documented in the appropriate place (README, docs/, or a CHANGELOG entry)
- [ ] Migration / rollout notes added to PR body if the change requires either
- [ ] Test evidence captured in PR body (commands and outcomes, not "tests pass")
- [ ] Self-review of the diff completed
- [ ] No secrets, no API keys, no host-specific paths in the diff (`git diff main...HEAD | grep -E 'sk-|password|/home/'` returns nothing)

## 6. HITL gates

The orchestrator may interrupt at any point with a `pause`, `redirect`, or `cancel` instruction. You must check for these between tasks.

You initiate HITL (per CLAUDE.md §6) when:

- A task is ambiguous (see CLAUDE.md §4)
- You've iterated on a failing test more than 3 times
- You'd add a new top-level dependency
- You'd touch `SPEC.md` itself (you don't — see §7)
- You'd run a destructive shell command
- You'd take longer than 30 minutes on a single task without progress to commit

The orchestrator initiates HITL when:

- A spec amendment lands and you must reload context (`spec-amend` arrives)
- An external blocker is identified (failing dependency upstream, etc.)
- The human has new information to inject

## 7. Spec amendments

You never edit `SPEC.md`. The amendment flow is (per [ADR 0004](https://github.com/yc-amerpus/dev-delivery/blob/main/decisions/0004-spec-amend-via-git-pull.md)):

1. If your work reveals that `SPEC.md` is wrong, **stop** and raise HITL with `question = "spec amendment proposed"`, `options = ["amend", "defer", "cancel-delivery"]`, and a clear summary of the proposed change in `context`.
2. The HITL response routes the question to Pete via the orchestrator. **Pete edits `SPEC.md` in the origin repo** (directly, or via a sub-flow that opens an amend-PR for him to merge).
3. Once Pete's edit is committed, the orchestrator dispatches a `spec-amend` instruction to you with the form `{kind: "spec-amend", delivery_id, ref: "<commit-sha-or-branch>", note: "..."}`.
4. On receipt of `spec-amend`:
   - Abort the current step at the next safe boundary (file write or shell command currently in flight is allowed to finish; no new step starts).
   - `git fetch origin && git checkout <ref>`. If the working tree has uncommitted changes, stash them with a label that includes `delivery_id` and the reason — never lose work silently.
   - Re-read `SPEC.md`, `TASKS.md`, `PROCESS.md` from the new ref.
   - Record the amendment in the active task's notes column (delivery_id + ref + note).
   - Resume work against the updated spec. If the change invalidates the current task, raise HITL asking how to proceed.

`spec-amend` arriving while a HITL episode is pending **supersedes** the episode (per [ADR 0001](https://github.com/yc-amerpus/dev-delivery/blob/main/decisions/0001-hitl-protocol.md)): the held call returns `decision: "spec-amended"` and you take the pull-and-reload path above. You then decide whether to re-raise the original question against the new spec.

This pattern keeps the spec stable as a contract — neither you nor the orchestrator should mutate it. The agent reads the spec; humans write it.

## 8. Artefacts

The `artefacts/` directory in the workspace is for things that need to live with the delivery but aren't code:

- `artefacts/decisions/<NNN>-<slug>.md` — short ADRs for non-trivial choices
- `artefacts/test-reports/` — captured test output for the PR's evidence
- `artefacts/diagrams/` — generated or sketched architecture diagrams

Commit these like any other file. They go in the PR.

## 9. Resource limits

A single task should not:

- Run shell commands for more than 5 minutes without producing progress (commits or HITL)
- Generate more than 10 commits without HITL check-in
- Open more than one PR (sub-task PRs to the delivery branch are exempt)

If you hit any of these, raise HITL with `status = "checkpoint-needed"` and let the orchestrator decide whether to continue.

## 10. End-of-delivery handover

When the delivery is complete:

1. PR is in `Ready for review`, CI is green, all DoD items ticked
2. `TASKS.md` is fully checked off
3. `artefacts/` contains test evidence and any decision records
4. Send `complete` status report to the orchestrator at `$ORCHESTRATOR_URL/status/complete` with the PR URL
5. Stop. The orchestrator handles merge, close, and container teardown.

If the merge introduces a regression or post-merge issue, the orchestrator will spin up a new delivery container with a follow-up SPEC. Do not try to "stick around" past the merge.
