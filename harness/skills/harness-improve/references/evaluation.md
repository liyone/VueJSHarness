# Lightweight harness evaluation

Read when designing or running a comparison. Use existing repository checks and review practices. These are evaluation procedures, not an instruction to install tooling or build an automated evaluation system.

## Historical replay

Use completed work to choose realistic tasks and discover recurring problems. Historical PR duration is not active engineering time, and merged code is not automatically the desired solution.

1. Choose a small representative set: ordinary feature work, a reactive defect, asynchronous ordering, a component contract, and a trivial edit. Include successes as well as failures. Six to ten cases is a useful starting point, not a statistical guarantee or required minimum.
2. Reconstruct each pre-change snapshot and the requirement/information available at that time. Check that the baseline environment works; separate setup failures from agent failures.
3. Keep later solutions, review discussions, commits, and evaluator-only checks out of the agent's accessible context. A worktree with later Git history still accessible is not an isolated historical snapshot. Use an isolated export or appropriately restricted checkout; give both variants the same usable environment.
4. Run the baseline and candidate in fresh, independent sessions. Keep the task, snapshot, model/version and reasoning settings, runtime/tools, dependencies, permissions, and effort budget equivalent. Preserve normal safety policies and existing repository instructions in both. Record any uncontrolled differences and solution leakage.
5. Establish acceptance criteria before inspecting generated solutions. Use independently reviewed behavior checks plus human review; allow valid alternative implementations. Evaluate the final artifact, not just the agent's success report or self-authored tests.
6. Repeat selected cases to expose variability. Retain failed, timed-out, and abandoned trials with their reasons. Report wins, ties, and losses by task and important regressions rather than only an average or best run.

Keep a few cases out of instruction tuning. Once a case is inspected to tune the harness, treat it as a regression case and replenish the unseen set. Replaying a known failure successfully demonstrates that repair, not general improvement.

## New work and team judgment

Use a bounded pilot on comparable live tasks after replay. Randomize or balance assignments across developers and task types where practical; record selection differences. Do not have the same developer repeat the same task and attribute their learning to the harness. Preserve normal delivery review in both conditions.

Compare anonymized baseline/candidate outputs where practical. Ask reviewers to identify a concrete advantage or defect and its consequence, not guess which used the harness. Resolve disagreements through the existing team decision process. Model grading may help triage, but calibrate it against human decisions; it must not define team policy or judge only obedience to the candidate rule.

| Signal | Record |
| --- | --- |
| Correctness | Acceptance checks, material defects, regressions, and subsequent fixes when observed. |
| Human effort | Active steering, review, correction, and verification time; separate queue/wait time. Mark unavailable measurements instead of estimating them from PR timestamps. |
| Team fit | Preferred output or tie, rationale, relevant accepted convention, and reviewer disagreement. |
| Scope and complexity | Concrete unnecessary changes, abstractions, dependencies, or ceremony. File count and line count alone are not defects. |
| Cost | Agent cost/elapsed time and maintenance effort where available, accounting for concurrent work. |

Agree on the important outcomes before comparison. Speed does not compensate for an unacceptable correctness regression. A small pilot supports a bounded decision, not a broad productivity claim. After adopting a change, examine actual review corrections and defects to check whether replay results transferred.

## Compact records

Keep these fields in an existing PR/issue or the requested local artifact. Reference evidence instead of duplicating private code or complete transcripts.

**Case/run record**

- Task and acceptance conditions; source issue/PR and pre-change revision.
- Baseline and candidate harness revisions (or exact local diffs when uncommitted); model/runtime/settings, tools, and effort budget.
- Check commands and outcomes; artifact locations, material corrections, human effort and cost when measured.
- Guidance retrieval evidence when diagnosing it; uncontrolled differences, missing inputs, and run disposition.
- Comparison decision and rationale; author/reviewer disagreement and verification limits.

**Feedback/change record**

- Observed behavior and evidence; accepted correction and rationale.
- Classification: correctness, team convention, project constraint, or individual preference; applicable scope and who accepted it, if known.
- Diagnosis and proposed smallest change; intended effect and exception boundary.
- Motivating, regression, and fresh-case results; unresolved evidence and measured tradeoffs.
- Team decision, adopted revision or pending local diff, and prior revision for rollback.

These records can be a few sentences. Do not demand every field for a trivial edit; include what is needed to assess the specific claim. Never label a proposed preference as accepted or a planned check as executed.
