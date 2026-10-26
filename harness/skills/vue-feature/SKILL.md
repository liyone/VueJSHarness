---
name: vue-feature
description: Implement a nontrivial Vue feature involving state, component coordination, or asynchronous behavior. Use for feature work, not routine text or style edits, defect investigation, or review-only requests.
---

# Vue feature

Deliver the requested behavior with clear ownership and the fewest useful moving parts. Follow the project's working agreement; keep planning proportional to the change.

1. Understand acceptance conditions and inspect the relevant feature, callers, contracts, dependencies, and available check commands. Clarify only ambiguities that materially affect behavior.
2. Read [architecture](../../../harness/standards/vue-architecture.md). Identify who owns state, what the main component coordinates, and whether a composable or presentational component earns its place. Simple local refs and computed values can stay local.
3. Choose the simplest implementation consistent with the supported Vue version. Explain a consequential tradeoff briefly; a separate planning document is unnecessary for small changes.
4. Implement the feature. Consult [reactivity](../../../harness/standards/vue-reactivity.md) for reactive or lifecycle changes and [reliability](../../../harness/standards/reliability.md) for requests, side effects, or user-facing failure handling. Read only applicable sections.
5. Handle relevant edge cases: invalid or empty input, errors, repeated actions, stale results, and unmounting. Use the [coordination example](../../../harness/examples/feature-architecture.md) only if it helps explain the boundary.
6. Apply the [testing decision guide](../../../harness/standards/testing.md), add valuable behavior coverage with existing tools, and run available checks appropriate to the change.
7. Review the final diff for regressions, accidental scope growth, and unnecessary indirection. Report implemented behavior, actual verification, and remaining limits.

These relative links work from the canonical directory and the documented discovery links; keep the installed directory layout intact.
