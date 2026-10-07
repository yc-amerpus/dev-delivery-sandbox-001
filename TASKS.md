# Tasks

> Sandbox backlog, phase-structured for the ADR 0014 phase-scoped-dispatch E2E
> (yc-amerpus/dev-delivery `decisions/0014`). Each phase carries an ADR 0014 §2
> status block so the orchestrator's phase index can parse it.

## Phase A — Sandbox smoke (phase-scoped dispatch)
- **Phase ID:** A
- **Status:** done
- **Depends on:** none

- [x] Append a single line reading `Phase-scoped dispatch verified.` to the end of `README.md`. Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer.

## Phase B — Follow-on (dependency + draft gate test)
- **Phase ID:** B
- **Status:** draft
- **Depends on:** A

- [ ] Placeholder task — not greenlit. Exists only to exercise the plan-gate dependency/draft warnings; do not dispatch.

## Phase C — Monitor observability run (F-02)
- **Phase ID:** C
- **Status:** done
- **Depends on:** A

- [x] Create `docs/monitor-check.md` containing a level-1 heading `# Monitor check` followed by a blank line and a single line reading `F-02 monitor verification — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer.

## Phase D — Monitor snapshot verification (F-02 criterion 1)
- **Phase ID:** D
- **Status:** done
- **Depends on:** C

- [x] Create `docs/monitor-check-2.md` containing a level-1 heading `# Monitor check 2` followed by a blank line and a single line reading `monitor_snapshot cadence verified — delivery {{delivery_id}}.` Then wait 180 seconds before reporting completion, so the D-04 monitor has time to emit several 60-second snapshots. Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer.

## Phase E — Audit chain verification (F-02 criteria 1 and 2)
- **Phase ID:** E
- **Status:** done
- **Depends on:** D

- [x] Create `docs/monitor-check-3.md` containing a level-1 heading `# Monitor check 3` followed by a blank line and a single line reading `monitor_snapshot, commit_observed and pr_opened verified — delivery {{delivery_id}}.` Then wait 150 seconds before reporting completion, so the D-04 monitor emits several 60-second snapshots after the commit. Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer.

## Phase F — Concurrent delivery, lane 1 (H-03)
- **Phase ID:** F
- **Status:** done
- **Depends on:** none

- [x] Create `docs/concurrent-f.md` containing a level-1 heading `# Concurrent lane F` followed by a blank line and a single line reading `H-03 concurrent delivery, lane F — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Then wait 240 seconds before reporting completion, so this delivery overlaps with the concurrent lane-G delivery. Touch no other file except this phase's own block in `TASKS.md`.

## Phase G — Concurrent delivery, lane 2 (H-03)
- **Phase ID:** G
- **Status:** done
- **Depends on:** none

- [x] Create `docs/concurrent-g.md` containing a level-1 heading `# Concurrent lane G` followed by a blank line and a single line reading `H-03 concurrent delivery, lane G — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Then wait 240 seconds before reporting completion, so this delivery overlaps with the concurrent lane-F delivery. Touch no other file except this phase's own block in `TASKS.md`.

## Phase H — Concurrent delivery re-run, lane 3 (H-03 / H-01)
- **Phase ID:** H
- **Status:** ready
- **Depends on:** none

- [ ] Create `docs/concurrent-h.md` containing a level-1 heading `# Concurrent lane H` followed by a blank line and a single line reading `H-03 re-run, two pending HITL gates, lane H — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Then wait 120 seconds before reporting completion. Touch no other file except this phase's own block in `TASKS.md`.

## Phase I — Concurrent delivery re-run, lane 4 (H-03 / H-01)
- **Phase ID:** I
- **Status:** done
- **Depends on:** none

- [x] Create `docs/concurrent-i.md` containing a level-1 heading `# Concurrent lane I` followed by a blank line and a single line reading `H-03 re-run, two pending HITL gates, lane I — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Then wait 120 seconds before reporting completion. Touch no other file except this phase's own block in `TASKS.md`.
