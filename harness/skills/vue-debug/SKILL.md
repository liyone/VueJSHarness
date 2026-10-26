---
name: vue-debug
description: Investigate and fix a Vue defect, especially reactive state, watcher, async timing, or lifecycle failures. Use for bug repair rather than feature design or review-only work.
---

# Vue debug

Find the cause before choosing the fix. Keep changes focused on the defect.

1. Reproduce the failure or characterize it precisely: expected versus observed behavior, inputs, timing, environment, and affected users. If reproduction is unavailable, separate evidence from hypotheses.
2. Trace the relevant code and data ownership. Check prop/ref reads, derived state, watcher dependencies, effect registration, request ordering, and unmount behavior where implicated. Inspect callers rather than treating an isolated symptom as the cause.
3. Read [reactivity](../../../harness/standards/vue-reactivity.md) for reactive or lifecycle defects, [reliability](../../../harness/standards/reliability.md) for asynchronous/error handling, or [architecture](../../../harness/standards/vue-architecture.md) if unclear ownership is causal. Load only the relevant guidance.
4. Test the most plausible explanation against evidence, then implement a focused fix. Avoid suppressing errors, adding watchers to conceal ownership problems, or performing unrelated refactoring.
5. Use the [testing decision guide](../../../harness/standards/testing.md) to add regression coverage when valuable. For timing defects, control completion order; for lifecycle defects, include invalidation or disposal. Prefer a check that fails for the original defect.
6. Verify the reported case and meaningful adjacent behavior with available checks. Inspect the final diff and report the cause, the fix, actual verification, and any unresolved uncertainty.
