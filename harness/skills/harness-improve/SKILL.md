---
name: harness-improve
description: Evaluate a coding harness against a baseline and improve its instructions or skills using observed outcomes and accepted team feedback. Use for harness evaluation, recurring review corrections, or team preference updates, not routine application implementation or code review.
---

# Evaluate and improve a harness

Optimize for correct, maintainable work with less human correction and unnecessary effort. Treat harness changes as testable hypotheses. A larger instruction set or better rule compliance alone is not evidence of better engineering.

## Establish the task

Determine whether the request is to compare harness versions, investigate feedback, or implement an improvement. Inspect the applicable agreement, affected skills/standards, and available evidence. Use the existing task authorization: prepare local candidate edits when improvements are requested; for evaluation-only requests, leave authoritative guidance unchanged. Do not add a recurring job, evaluation platform, packages, or publication workflow without a concrete request.

Identify the baseline, target repository/revision, harness revision, and relevant task or review evidence. Ask only for missing information that materially blocks the work; continue independent analysis where possible. If the application or historical evidence is unavailable, prepare a concrete comparison plan and identify the missing inputs. Do not invent runs, measurements, consensus, or historical outcomes.

## Diagnose before changing instructions

For each material observation, record the affected behavior, evidence location, accepted correction and rationale, and scope. Distinguish:

- Missing guidance from guidance that was not retrieved.
- Ambiguous/conflicting guidance from failure to follow clear guidance.
- A harness issue from a task, environment, tool, or application defect.

Inspect available tool records or session context to establish what was loaded; an agent's assertion alone is insufficient. Unknown retrieval remains unknown. If an existing rule already addresses the issue, consider routing, clarity, or an example before adding another rule.

Separate correctness requirements, agreed team conventions, project constraints, and individual tastes. Repeated accepted corrections or one serious incident can justify a candidate; frequency alone does not establish consensus. Keep unresolved preferences as questions. Scope project-specific knowledge to that project instead of copying it into reusable guidance. For a Vue dependency decision, consult [architecture](../../../harness/standards/vue-architecture.md) only if relevant.

## Compare or prepare a candidate

For historical replay, live trials, metrics, or evaluation records, read [evaluation.md](references/evaluation.md). Match the effort to the claim: a narrow wording correction needs targeted verification; a claimed productivity improvement needs comparative evidence.

State the hypothesis in observable terms: which behavior should improve, under what conditions, and what must remain unaffected. Edit the smallest authoritative section, improve an example, narrow a rule, or remove redundant guidance. Include an exception boundary where it changes the decision. Preserve authoritative homes and existing skill responsibilities; do not turn feedback into an ever-growing startup handbook.

Use the current harness as the baseline for a candidate improvement. For initial adoption, compare the team's usual agent setup with that same setup plus the harness. Run authorized comparisons in isolated workspaces. Include the motivating case, relevant regressions, and ordinary/trivial work where added process could hurt. Reserve fresh cases from tuning and repeat noisy trials when needed; never cherry-pick the best run.

## Review and retain the result

Check metadata, references through source and discovery paths, instruction consistency, startup size, and the final diff as applicable. Verify behavior with the target project's existing checks; the [testing guide](../../../harness/standards/testing.md) covers application test decisions. Separate static review, simulated exercises, and executed application checks.

Present the candidate and evidence for the team's existing review process. Follow any already-authorized adoption scope; do not ask for approval again unnecessarily. Feedback alone does not authorize merging, publishing, or silently replacing accepted policy. Human decisions establish team preferences; an automated or model-based score can assist but cannot supply missing consensus.

Report:

- Observation, evidence, diagnosis, and intended scope.
- Proposed/implemented change and expected behavioral effect, including deliberate non-changes.
- Baseline/candidate identities, checks actually run, results, and limitations; mark unmeasured impact explicitly.
- Recommendation: adopt, revise, reject, or collect specific missing evidence, with the decision still needed and the previous revision to retain for rollback.

Use the existing PR or issue to retain this compact record rather than generating a new reporting system. Capture approved preferences in their authoritative guidance. At later reviews, revisit rules that cause extra work, no longer address a current problem, or became redundant after a model/runtime change. Test removing them. A no-change recommendation is a valid outcome.
