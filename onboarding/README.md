# Microservice codebase onboarding

A portable tutoring skill for understanding an existing system across repositories. Start from your key services or layers, learn their immediate upstream and downstream neighbors deeply, and keep farther services as context. Lessons explain the system and actual code before offering practical scenarios, using TypeScript to bridge unfamiliar languages.

[SKILL.md](SKILL.md) owns the teaching workflow. This folder is independent of the Vue harness: copy it as a unit, without `harness/`, the project instruction template, or a personal skill installation. It contains instructions, not an application, scanner, or guarantee of learning proficiency.

## Start with either assistant

Copy `onboarding/` into the parent workspace containing the code repositories, or keep it in an accessible shared checkout and supply its path. Open Claude Code or Codex at the parent workspace with access to the repositories you want to study. Do not assume a session opened in one service can inspect all its siblings.

Use this portable prompt, replacing the example values and adjusting the skill path if you kept the bundle elsewhere:

```text
Read onboarding/SKILL.md and use it to onboard me to this system.

Workspace: <parent folder containing the repositories>
Key repositories or services: <for example orders-api and fulfillment-worker>
Priority layers: <optional concrete UI, BFF, or backend services>
Business journey, endpoint, or event: <optional starting topic>

Use these as the starting set. Follow the skill's discovery, teaching,
depth, and progress workflow. Begin with the system orientation, then
the first recommended journey. Keep learning notes in this workspace.
```

The bracketed values are inputs, not command syntax. Key repositories can contain multiple services; name a service or path if you want to narrow the starting set. Omit optional fields you do not need. If you do not know where to start, ask the tutor to inspect the workspace and recommend starting points.

For a new conversation:

```text
Read onboarding/SKILL.md and resume onboarding for <workspace path>.
Use onboarding-notes/progress.md, check the relevant source changes,
and continue with the next recommended lesson.
```

You can also say "explain this line," "show the TypeScript equivalent," or "add checkout-bff to our starting set." Supply both the skill's contents and the relevant source material if the assistant cannot read local files. A pasted starter prompt alone cannot provide repository access. The teaching instructions remain the same for other assistants; their file access and discovery capabilities vary.

Learning notes belong in the target workspace's `onboarding-notes/`, alongside the repositories and separate from this reusable folder. They retain the system map, source evidence, coverage boundary, glossary, journeys, questions and learning progress. Existing notes support continuation; they are checked against current sources when resuming.

## Optional project-local discovery

Explicit-file usage above needs no discovery setup. For named invocation, keep this folder authoritative and link it from the host's discovery directory **at the workspace where you start the session**:

| Host | Discovery link | Invocation after discovery |
| --- | --- | --- |
| Claude Code | `.claude/skills/onboarding` | `/onboarding` plus the workspace and starting set |
| Codex | `.agents/skills/onboarding` | `$onboarding` plus the workspace and starting set |

Both hosts document linked skill directories: [Claude Code skills](https://code.claude.com/docs/en/skills#choose-where-skills-load) and [Codex skills](https://learn.chatgpt.com/docs/build-skills#where-codex-loads-local-skills). The skill uses the portable `name` and `description` fields from the [Agent Skills specification](https://agentskills.io/specification). Provider syntax stays here, outside the teaching instructions.

Run the following from the workspace root **after copying this folder there**. Choose only the discovery directory for the host you use, or keep both. Existing destinations must be inspected rather than replaced.

On macOS/Linux:

```sh
for discovery_dir in .claude/skills .agents/skills; do
  mkdir -p "$discovery_dir"
  if [ -e "$discovery_dir/onboarding" ] || [ -L "$discovery_dir/onboarding" ]; then
    echo "Already exists: $discovery_dir/onboarding" >&2
    exit 1
  fi
  ln -s ../../onboarding "$discovery_dir/onboarding" || exit 1
done
```

On Windows PowerShell:

```powershell
$skillTarget = (Resolve-Path -LiteralPath 'onboarding' -ErrorAction Stop).Path
foreach ($discoveryDir in @('.claude/skills', '.agents/skills')) {
    $discoveryLink = Join-Path $discoveryDir 'onboarding'
    if (Get-Item -LiteralPath $discoveryLink -Force -ErrorAction SilentlyContinue) {
        throw "Already exists: $discoveryLink"
    }
    New-Item -ItemType Directory -Path $discoveryDir -Force -ErrorAction Stop | Out-Null
    New-Item -ItemType SymbolicLink -Path $discoveryLink -Target $skillTarget -ErrorAction Stop
}
```

Windows symbolic links may require Developer Mode or permission to create them. This PowerShell example uses an absolute link target: recreate these local links after moving or cloning the workspace, and keep machine-specific link targets out of commits. If links are unavailable, use the explicit-file prompt; do not maintain a second copy of the skill instructions for discovery.

Start a fresh session at the parent workspace. Check the host's skill listing, invoke onboarding, and verify that it reads the intended `SKILL.md` and can inspect the named repositories. A plain `onboarding/` folder is not itself a host discovery directory. Starting inside a nested service repository can change the discovery boundary; return to the parent workspace or use the explicit path. Local Claude Code setup does not establish support in Claude chat, Cowork, or hosted sessions.

## Validation and limits

For changes to this bundle, validate skill metadata, relative references, the copied folder, and discovery-link targets. Exercise the workflow against a small multi-repository example with UI/BFF requests, incoming and outgoing events, fan-out, multiple starting points, a cycle, shared libraries, ambiguous event names, missing services, and a source change before resume. Check the scope map, evidence, explanations, unanswered exercise, and progress notes rather than matching prescribed wording.

Keep structural checks, author walkthrough trials, independent host tests, runtime execution, and learner outcomes distinct. A source walkthrough does not prove that services execute correctly, that both hosts discover the skill, or that a learner is proficient.

Host discovery documentation checked on **2026-11-08**. Actual validation outcomes are reported with the change; the commands above are setup instructions, not a claim that either assistant was launched and tested.
