---
name: vue-review
description: Review Vue implementation changes for actionable correctness defects, regressions, unsafe reactive or async behavior, and unnecessary complexity. Use for review requests rather than implementation or general style advice.
---

# Vue review

Review the change against its intended behavior and relevant surrounding contracts. Read the diff, affected callers, and existing tests; do not infer a defect from an isolated line alone. Do not edit files unless the request includes fixes.

Consult only the relevant parts of [architecture](../../../harness/standards/vue-architecture.md), [reactivity](../../../harness/standards/vue-reactivity.md), [reliability](../../../harness/standards/reliability.md), and [testing](../../../harness/standards/testing.md).

Focus on:

- Actual correctness defects or plausible regressions with a concrete trigger.
- Lost reactivity, prop mutation, unclear state ownership, or incorrect watcher/lifecycle behavior.
- Stale request results, missing failure handling, leaked effects, or misleading loading/empty states.
- Unnecessary abstractions or responsibility boundaries that materially obscure the change.
- Significant accessibility or security issues in the affected flow.
- Missing tests for important changed behavior, where existing coverage does not protect it.

Prioritize findings by user impact and likelihood. For each finding give its location, trigger, consequence, evidence, and a focused correction. Mark uncertainty and explain how to confirm it. Keep questions separate from confirmed defects.

Avoid speculative lists, arbitrary style preferences, coverage quotas, and file splitting based on size. Report when no actionable findings remain, along with verification limits; absence of findings is not proof of correctness.
