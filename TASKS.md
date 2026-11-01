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
- **Status:** done
- **Depends on:** none

- [x] Create `docs/concurrent-h.md` containing a level-1 heading `# Concurrent lane H` followed by a blank line and a single line reading `H-03 re-run, two pending HITL gates, lane H — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Then wait 120 seconds before reporting completion. Touch no other file except this phase's own block in `TASKS.md`.

## Phase I — Concurrent delivery re-run, lane 4 (H-03 / H-01)
- **Phase ID:** I
- **Status:** done
- **Depends on:** none

- [x] Create `docs/concurrent-i.md` containing a level-1 heading `# Concurrent lane I` followed by a blank line and a single line reading `H-03 re-run, two pending HITL gates, lane I — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Then wait 120 seconds before reporting completion. Touch no other file except this phase's own block in `TASKS.md`.

## Phase J — D-05 merge-path verification (#81 NCT progress + out-of-band reconcile)
- **Phase ID:** J
- **Status:** done
- **Depends on:** none

- [x] Create `docs/merge-path-check.md` containing a level-1 heading `# Merge path check` followed by a blank line and a single line reading `D-05 NCT merge-progress and out-of-band reconcile verified — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Touch no other file except this phase's own block in `TASKS.md`.

## Phase K — Full nine-event chain on a FAST delivery (#138)
- **Phase ID:** K
- **Status:** done
- **Depends on:** none

- [x] Create `docs/nine-event-check.md` containing a level-1 heading `# Nine event check` followed by a blank line and a single line reading `pr_opened emitted authoritatively on a fast delivery — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Do not wait or sleep at any point — finish as fast as you can, because the point of this phase is a short-lived PR. Touch no other file except this phase's own block in `TASKS.md`.

## Phase L — Archive completeness (#140)
- **Phase ID:** L
- **Status:** done
- **Depends on:** none

- [x] Create `docs/archive-check.md` containing a level-1 heading `# Archive check` followed by a blank line and a single line reading `teardown_complete now lands in the per-delivery archive — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Do not wait or sleep at any point. Touch no other file except this phase's own block in `TASKS.md`.

## Phase Q — Resume live check (#202)
- **Phase ID:** Q
- **Status:** ready
- **Depends on:** none
- **Executor:** dd
- **Requires:** none

- [ ] Q-01: BEFORE doing anything else, ask the operator a question using the orchestrator `/hitl` endpoint exactly as described in DELIVERY_PROTOCOL.md ("If you need a human decision"). Use task id `Q-01`, question `Q-01: which colour should docs/resume-smoke.md record?`, options `red` and `blue`. The call long-polls: when it returns `{"decision":"keepalive",...}` you MUST immediately re-POST the same request with the returned `continuation` token, and keep doing so until you get a real decision. If a call fails without a response (curl exit 7, 52 or 56, or HTTP 502/503/504) the orchestrator is restarting: sleep 15 seconds and re-POST the identical request with the last `continuation` you received, and keep retrying; never give up and never ask a new question. Do not create files, commit, or push before you have a real decision.
- [ ] Q-02: Then ask a second question the same way: task id `Q-02`, question `Q-02: which shape should docs/resume-smoke.md record?`, options `circle` and `square`, with the same keepalive and retry rules. The operator will stop your process while you wait for this answer and then resume you: if your prompt says you were resumed and names a `continuation` for a request, re-POST that request with that continuation exactly as the prompt says, and use the decision it returns. Never ask Q-01 or Q-02 a second time as a new question.
- [ ] Q-03: Then create `docs/resume-smoke.md` with a level-1 heading `# Resume smoke` followed by a blank line and two lines: `colour: <Q-01 decision>` and `shape: <Q-02 decision> — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Touch no other file except this phase's own block in `TASKS.md`. (The operator will cancel this delivery at the merge gate; that is expected.)

## Phase M — agent_silent live check (#144)
- **Phase ID:** M
- **Status:** ready
- **Depends on:** none

- [ ] BEFORE doing anything else, ask the operator a question using the orchestrator `/hitl` endpoint exactly as described in DELIVERY_PROTOCOL.md ("If you need a human decision"). Use task id `M-01`, question `Which wording should docs/silent-check.md use?`, options `alpha` and `beta`. The call long-polls: when it returns `{"decision":"keepalive",...}` you MUST immediately re-POST the same request with the returned `continuation` token, and keep doing so until you get a real decision. If a call fails without a response (curl exit 7, 52 or 56, or HTTP 502/503/504) the orchestrator is restarting: sleep 15 seconds and re-POST the identical request with the last `continuation` you received, and keep retrying; never give up and never ask a new question. Do not create files, commit, or push before you have a real decision. Then create `docs/silent-check.md` with a level-1 heading `# Silent check` and one line `wording: <decision> — delivery {{delivery_id}}.` Commit with a Conventional Commit message and a `Refs: {{delivery_id}}` trailer, push, and open the PR. Touch no other file except this phase's own block in `TASKS.md`.
