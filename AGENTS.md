# Maintaining this harness

This repository contains reusable engineering instructions, not an application. Keep v0.1 useful through small, evidence-based improvements. Start with [README.md](README.md) for the layout and adoption contract; consult other files only when the change needs them.

## Principles and scope

- Prioritize correctness, readability, maintainability, consistency, testability, productivity, then meaningful performance.
- Prefer explicit, cohesive guidance. Avoid speculative abstractions, mandatory ceremony, arbitrary file-length limits, and rules that merely repeat an agent's general capabilities.
- Do not scaffold an application, install dependencies, or create or change package manifests to maintain this harness. Keep examples illustrative.
- Preserve one authoritative home for each standard and skill. Discovery links and the Claude import are adapters, not independent instruction sets.
- Keep the project instruction template framework-neutral apart from pointers to relevant guidance. Keep vendor syntax in adapters or integration documentation.

## Working workflow

1. Understand the requested outcome and inspect the affected guidance and its callers. Existing text is evidence, not proof that a rule is good.
2. Verify technical claims against official documentation. The supported Vue baseline is 3.5; current documentation may describe later APIs.
3. Make the smallest cohesive change. Update related links or examples when their contracts change, without unrelated cleanup.
4. Keep startup instructions around 400–700 tokens. Put task-specific detail in the relevant standard or skill rather than automatically loading every document.

## Verification and handoff

Check skill frontmatter, relative references in both source and installed layouts, the three manual evaluation tasks, and the final diff. Review examples for state ownership, lifecycle and asynchronous errors when relevant. Report checks actually run, outcomes, and missing tooling; never imply an application build or test ran here.

The [project template](template/AGENTS.md) is the adoption artifact. [Standards and skills](README.md#layout) define project work; they are not instructions to build a Vue application in this repository. Update the smallest authoritative section after a demonstrated problem, and keep integration setup in README rather than expanding startup context.
