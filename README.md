# Dotnet Lift Skills

Reusable AI agent skills for upgrading legacy .NET applications to modern .NET. Dotnet Lift guides a migration from repository discovery through planning and controlled execution, then hands off to an independent validation skill.

The skills use the open `SKILL.md` format and can be added to projects that use GitHub Copilot, Claude Code, or OpenAI Codex.

This repository is also packaged as an installable plugin for Claude Code and GitHub Copilot CLI. Both plugin packages discover the same `skills/` directory, so the skill definitions stay in one place.

## First-time install (recommended)

The easiest way to install Dotnet Lift is with the `npx skills` command. It downloads the skills into your project and can configure them for Claude Code, GitHub Copilot, Codex, and other supported agents. You do not need to clone this repository or install the CLI globally.

### 1. Open your .NET project

Open a terminal in the folder containing the .NET project you want to upgrade. For example, in PowerShell:

```powershell
cd C:\path\to\your-dotnet-project
```

### 2. Install Dotnet Lift

Run:

```sh
npx skills add sajagjain/dotnet-lift
```

If prompted, select all five skills to use the complete workflow, then select the agent or agents you use. If you only want a subset, select the skills you need; `lift-flow` works best when all five are installed together.

The command requires Node.js/npm so that `npx` is available. If your terminal says `npx` is not recognized, install Node.js from [nodejs.org](https://nodejs.org/) and reopen the terminal.

### 3. Start the workflow

Open or restart your AI coding-agent session in that project, then ask it to use `lift-flow` to upgrade the application. You can also invoke a single skill, such as `lift-prep`, to begin with readiness discovery.

To confirm which skills were installed, run:

```sh
npx skills list
```

See the [`npx skills` CLI documentation](https://www.skills.sh/docs/cli) for more options.

## Skills

| Skill | Purpose |
| --- | --- |
| [`lift-flow`](skills/lift-flow/SKILL.md) | Orchestrates the full workflow and coordinates the other skills. |
| [`lift-prep`](skills/lift-prep/SKILL.md) | Discovers the application and creates `UPGRADE_READINESS.md`. |
| [`lift-plan`](skills/lift-plan/SKILL.md) | Turns readiness findings into an approved, actionable `UPGRADE_PLAN.md`. |
| [`lift-run`](skills/lift-run/SKILL.md) | Executes an approved migration incrementally, with human approval gates. |
| [`lift-check`](skills/lift-check/SKILL.md) | Independently validates the migration and reports regressions. |

Use the skills individually, or start with `lift-flow`. The workflow deliberately keeps migration execution behind explicit approval. Independent validation is a separate, user-initiated step.

## Install as a plugin

### Claude Code

In Claude Code, add the marketplace and install the plugin:

```text
/plugin marketplace add sajagjain/dotnet-lift
/plugin install dotnet-lift@dotnet-lift-marketplace
```

Or use the CLI:

```sh
claude plugin marketplace add sajagjain/dotnet-lift
claude plugin install dotnet-lift@dotnet-lift-marketplace
```

### GitHub Copilot CLI

Register the marketplace and install the plugin:

```sh
copilot plugin marketplace add sajagjain/dotnet-lift
copilot plugin install dotnet-lift@dotnet-lift-skills
```

Copilot CLI plugin installation is separate from availability in other Copilot clients. See the [Copilot plugin documentation](https://docs.github.com/en/copilot/concepts/customizations/plugins) for supported surfaces and current setup steps.

## Copy skills into a project

You can also use the skills without installing a plugin. Copy the `skills/` directory into the agent skills directory supported by your tool:

| Tool | Project-level skills directory |
| --- | --- |
| GitHub Copilot | `.github/skills/` |
| Claude Code | `.claude/skills/` |
| OpenAI Codex | `.agents/skills/` |

For example, to use `lift-prep` with Copilot, copy `skills/lift-prep/` to `.github/skills/lift-prep/`. Each skill folder must retain its `SKILL.md` file. Repeat for the skills you want available; copy all five to use the complete workflow. Check your agent tool's documentation for any changes to supported skill locations or discovery behavior.

Then open the target .NET repository in your agent and invoke the desired skill by name, such as `lift-prep` or `lift-flow`.

## Workflow

1. **Prepare** — inspect the application and write `UPGRADE_READINESS.md`.
2. **Plan** — evaluate migration options and produce `UPGRADE_PLAN.md` with acceptance criteria.
3. **Run** — verify explicit approval, execute the approved tasks, and record progress.
4. **Check** — in a fresh session, independently inspect the result and produce validation and regression reports.

The orchestrator also tracks workflow state in `UPGRADE_STATUS.md`. Skills are designed to preserve existing functionality and to surface assumptions, risks, unavailable dependencies, and decisions that need human input.

## Requirements and limitations

- These skills provide instructions to an AI agent; they do not themselves install .NET SDKs, execute migrations, or guarantee a successful result.
- The agent needs access to the target repository and the tools required to inspect, build, and test that application.
- Review generated plans and changes before approving or applying them. Availability of framework versions, packages, infrastructure, and agent capabilities may affect results.
- `lift-flow` expects the other skill folders to be available to the agent when it coordinates the workflow.

## Contributing

Issues and pull requests are welcome. Keep skill names short, ensure each directory name matches its `SKILL.md` frontmatter `name`, and update cross-skill references when changing the workflow.

## License

This project is licensed under the [MIT License](LICENSE).
