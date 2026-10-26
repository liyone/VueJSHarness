# Vue engineering harness v0.1

A small, reusable coding agreement for engineers and agents working in an existing Vue 3.5 application. It aims to improve correctness and consistency without adding application code, packages, or mandatory process. This repository is a documentation bundle, not a Vue starter.

Favor a readable feature component, meaningful stateful logic in a composable, and presentational components when they earn their place. Keep simple local state local. Extract by responsibility and practical benefit, never by file length. Existing application code is context to inspect, not automatically architecture to imitate.

## Layout

```text
AGENTS.md                         Maintaining this harness
README.md                         Adoption and integration
template/AGENTS.md                Working agreement to adopt in a project
harness/
  standards/
    vue-architecture.md           Ownership, boundaries, dependency decisions
    vue-reactivity.md             Vue 3.5 state, effects, lifecycle
    reliability.md                Requests, races, errors, security, accessibility
    testing.md                    Behavior and risk-based test decisions
  skills/
    vue-feature/SKILL.md           Implement a nontrivial feature
    vue-debug/SKILL.md             Investigate and fix a defect
    vue-review/SKILL.md            Review implementation changes
  examples/feature-architecture.md
adapters/claude/CLAUDE.md           Imports the adopted AGENTS.md
```

`harness/` is one portable payload. The four standards own technical guidance; the three skills own task workflows and link to those standards. The example demonstrates their use. The root and template `AGENTS.md` serve different repositories: maintenance here, application work after adoption.

## Progressive disclosure

Start with the small project `AGENTS.md` and, where supported, skill names and descriptions. Load a relevant skill when undertaking its task. Follow its links only for the behavior being changed: architecture for boundaries, reactivity for reactive defects, reliability for asynchronous work, testing for test decisions. A wording fix needs none of this ceremony.

Skill files use only the portable `name` and `description` frontmatter fields from the [Agent Skills specification](https://agentskills.io/specification). Provider commands live in this README and the import adapter, not in canonical skills. Discovery behavior belongs to the host, as described by the [Agent Skills client guide](https://agentskills.io/client-implementation/adding-skills-support).

## Adopt in an existing Vue 3.5 repository

Use a normal review branch in the application repository. Do not copy this repository's root maintenance instructions into it.

| Source here | Destination in the application |
| --- | --- |
| `harness/` directory | `<project>/harness/` with its contents unchanged |
| `template/AGENTS.md` | `<project>/AGENTS.md` |
| `adapters/claude/CLAUDE.md`, if using Claude Code | `<project>/CLAUDE.md` |

If a destination already exists, review and merge it rather than overwrite it. Preserve actual project constraints and commands; remove duplicated agreements. In an existing `CLAUDE.md`, retain unique configuration and add `@AGENTS.md` once. The adapter imports its adjacent file **after copying to the project root**; it is not meant to run in `adapters/claude/` here.

Confirm the application's Vue runtime and SFC compiler support 3.5. Record its real lint, typecheck, test, and build commands in the adopted agreement when available; do not invent commands or install tooling to make the list complete. Keep the first adoption bounded to one feature or defect, with no bulk architectural rewrite.

Keep `harness/` at the project root. Skill links such as `../../../harness/standards/reliability.md` work from all three equal-depth locations: `harness/skills/<name>`, `.agents/skills/<name>`, and `.claude/skills/<name>`. Template pointers are explicitly project-root-relative. If relocating the bundle, update and recheck these references together.

## Codex and Claude Code discovery

The authoritative skill bodies stay in `harness/skills/`. Add one directory symlink per skill under the discovery path for the agent you use:

| Host | Discovery directory | Explicit use |
| --- | --- | --- |
| Codex | `.agents/skills/` | `$vue-feature`, `$vue-debug`, `$vue-review` |
| Claude Code | `.claude/skills/` | `/vue-feature`, `/vue-debug`, `/vue-review` |

Both hosts document support for linked skill directories. Codex reads project `AGENTS.md`; the Claude adapter imports the same agreement. Skills can also be selected by relevance. See [Codex instructions](https://learn.chatgpt.com/docs/agent-configuration/agents-md), [Codex skills](https://learn.chatgpt.com/docs/build-skills), [Claude instructions and imports](https://code.claude.com/docs/en/memory), and [Claude skills](https://code.claude.com/docs/en/skills).

On macOS/Linux, from the **application root**, run the following for both hosts, or omit the discovery directory you do not use. Destinations must be absent; inspect collisions rather than replacing existing skills.

```sh
mkdir -p .agents/skills .claude/skills
for name in vue-feature vue-debug vue-review; do
  ln -s "../../harness/skills/$name" ".agents/skills/$name"
  ln -s "../../harness/skills/$name" ".claude/skills/$name"
done
```

On Windows, directory symbolic links require Developer Mode or permission to create them. This PowerShell equivalent uses absolute targets, so recreate these local links when cloning or moving the checkout; do not distribute machine-specific link targets. Choose just one discovery directory if appropriate.

```powershell
foreach ($discoveryDir in @('.agents/skills', '.claude/skills')) {
    New-Item -ItemType Directory -Path $discoveryDir -Force | Out-Null
    foreach ($skillName in @('vue-feature', 'vue-debug', 'vue-review')) {
        $skillTarget = (Resolve-Path "harness/skills/$skillName").Path
        New-Item -ItemType SymbolicLink -Path "$discoveryDir/$skillName" -Target $skillTarget
    }
}
```

If link creation is unavailable, keep the single canonical bundle and ask the agent to read `harness/skills/vue-feature/SKILL.md` (or the other named skill) explicitly. This fallback supplies the workflow without promising automatic discovery. Do not maintain second copies of skill bodies.

Start a fresh agent session in the application root. Ask it to identify the active agreement, list the three skills, and open one linked standard. Confirm the result against the files and the host's skill listing, not only its assertion. Check for conflicting global/nested instructions or an existing skill with the same name if discovery is unexpected. In Claude, `/memory` helps inspect loaded instructions.

## Claude Code using Amazon Bedrock

Use the same files and discovery setup. Claude Code still supplies the agent runtime; Bedrock supplies model inference. With your organization's existing AWS authentication, select an approved region and model or inference profile as described in the [official Bedrock setup](https://code.claude.com/docs/en/amazon-bedrock).

For example, set these for the current PowerShell session, replacing the placeholders:

```powershell
$env:CLAUDE_CODE_USE_BEDROCK = '1'
$env:AWS_REGION = '<approved-region>'
$env:AWS_PROFILE = '<existing-profile>'
$env:ANTHROPIC_MODEL = '<approved-model-or-inference-profile-id>'
claude
```

`AWS_PROFILE` applies when using a named profile; follow the organization's credential method otherwise. AWS credentials, Bedrock access, IAM permissions, and regional model availability are prerequisites, not supplied by this harness. Keep credentials out of repository files. Check `/status` for the provider and region, then verify instruction and skill discovery as above. No model ID is pinned here because account availability changes.

## Custom coding agents using Bedrock APIs

A raw model request does not discover repository files. The runtime must provide the context and tools. The [Converse API](https://docs.aws.amazon.com/bedrock/latest/APIReference/API_runtime_Converse.html) exposes `system`, `messages`, and `toolConfig`; [client-side tool execution](https://docs.aws.amazon.com/bedrock/latest/userguide/tool-use.html) is the application's responsibility.

The minimal integration contract is:

1. Resolve the project root, read the approved `AGENTS.md`, and supply it as repository instructions alongside the task. Define how scoped instructions are applied; do not assume a particular product's discovery semantics.
2. Enumerate `harness/skills/*/SKILL.md` and initially supply only each name, description, and path. Load the selected skill body when needed.
3. Let the agent read relevant source and linked standards through authorized file tools, or have the runtime retrieve that context. Preserve the original path/base when presenting files so relative references resolve correctly. Keep needed instructions across subsequent requests and context compaction.
4. Execute authorized file edits and checks in the runtime, return actual tool results, and apply its permission boundaries. Prompt text is not a sandbox or an approval mechanism.

This is a context contract, not an orchestration implementation. For an API without the same fields, use its documented equivalents. The same Markdown remains authoritative.

## Three manual evaluation tasks

Try these in an existing application branch with its tools and API contracts. They evaluate the harness's usefulness; they are not an automated benchmark or an accuracy claim.

| Task prompt | What to inspect |
| --- | --- |
| **Feature:** "Add passenger name search to this feature using the existing API. Show loading, failure, and no-results states; allow selecting a passenger." | Agent inspects the contract, assigns ownership, uses only useful boundaries, and adds no unjustified package. Check blank input, success, failure, keyboard operation, and meaningful existing tests. Compare with the [example](harness/examples/feature-architecture.md), without requiring its exact file layout. |
| **Debug:** "A slow earlier passenger search overwrites the latest result. Reproduce and fix it; leaving the page must clean up pending work." | Control A/B completion order and late rejection as well as success. Verify the latest query owns results, error, and loading; unmount invalidates work. Expect a focused cause and regression check, no unrelated rewrite. |
| **Review:** "Review this passenger feature change for release risks. It adds a prop-backed editable name, a request watcher, and a custom clickable row." | Supply the actual diff and callers. Look for evidence of prop mutation, stale/uncleaned async writes, and inaccessible interaction, when present. Findings need a concrete trigger and correction; a large cohesive file alone is not a defect. Expect a small prioritized list or an honest no-findings report. |

For each run, note whether the right guidance was retrieved, behavior was correct, checks really ran, and any rule caused extra effort without benefit. Compare a baseline and harness-assisted run where practical; three examples alone cannot establish effectiveness.

## Maintain and validate

Change a standard after a recurring defect or a real feature reveals a gap. Keep the technical rule in its owning document; skills should link to it. Add another skill or reference only when a distinct recurring need warrants it. Record the adopted harness revision in the application's adoption commit or review, and update the bundle through normal diff review.

When changing this repository, check:

- Root and template instructions remain small (target roughly 400–700 tokens each; tokenization varies).
- Exactly three skills have valid `name`/`description` metadata matching their folders.
- Links work both here and through the documented installed paths; the Claude import resolves after adoption.
- Standards and examples agree, use Vue 3.5-compatible APIs, and keep dependency decisions explicit.
- The full diff, including new files, introduces no app, package manifest, or unrelated tooling.

Official documentation was checked on **2026-10-25**; sources appear beside the relevant guidance. There is no configured application build, typecheck, or test suite in this documentation-only repository. Structural review does not prove the illustrative Vue snippets execute correctly or that every host/account configuration works; run the manual checks after adoption.

v0.1 deliberately omits installation automation, extra npm packages, a marketplace, orchestration, automated evaluation, CI infrastructure, and configuration frameworks. The most valuable next step is to use it on one real feature, record defects and friction, and make the smallest evidence-based correction.
