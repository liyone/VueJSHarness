# Working agreement

Prioritize correctness, readability, maintainability, consistency, testability, developer productivity, then performance where it matters. Prefer explicit, cohesive code and the simplest correct solution. Judge files by responsibility and understandability, never line count. Extract abstractions only for a concrete benefit; keep simple local behavior together.

## Before and during a change

- Understand the requested behavior, acceptance conditions, and important constraints. Inspect relevant code, callers, configuration, and existing checks before editing. Existing code is context, not automatically an architectural example to copy.
- Consult only the guidance relevant to this task. Choose a focused skill for substantial feature work, debugging, or review; routine edits need no formal plan or planning document.
- Identify state ownership and side-effect lifetimes. Preserve behavior outside the request. Avoid unrelated refactoring, speculative layers, and premature optimization.
- Prefer platform capabilities and suitable existing dependencies. Adding a package requires an engineering justification and approval; use the dependency decision in the architecture guidance.
- Respect the repository's actual framework version, contracts, and conventions where sound. Explain a material conflict instead of silently copying a known defect or broadening scope.

## Verify and report

Use existing checks appropriate to the change, inspect the final diff, and consider meaningful failure cases and regressions. Add tests for important behavior and risks, not trivial implementation details or a coverage target. If no automated test or typecheck command exists, report that limitation. State what changed, what actually ran, its result, and any remaining uncertainty. Never claim a check passed without running it.

## Retrieve guidance as needed

Paths below are relative to the **project root**, after adoption. They are pointers, not an instruction to load everything.

| Task | Guidance |
| --- | --- |
| Feature structure, boundaries, dependencies | `harness/standards/vue-architecture.md` |
| Reactive state, computed values, watchers, lifecycle | `harness/standards/vue-reactivity.md` |
| API calls, races, errors, security, accessibility | `harness/standards/reliability.md` |
| Test decisions and regression coverage | `harness/standards/testing.md` |
| Nontrivial feature | `harness/skills/vue-feature/SKILL.md` |
| Defect investigation and repair | `harness/skills/vue-debug/SKILL.md` |
| Implementation review | `harness/skills/vue-review/SKILL.md` |

For a concrete coordination example, read `harness/examples/feature-architecture.md` only when useful.
