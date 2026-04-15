![RocketSim Agent Skill](assets/banner.jpg)

# RocketSim Agent Skill

The RocketSim Agent Skill is a companion skill for [RocketSim](https://www.rocketsim.app) that gives AI coding tools a reliable, stateful way to inspect and interact with iOS Simulator apps.

For the latest documentation on agentic coding with RocketSim, we encourage you to read the [RocketSim documentation](https://www.rocketsim.app/docs/features/agentic-development/).

This repository contains only the installable skill assets, so tools like `skills.sh`, Claude Code, Cursor, and OpenAI-compatible agents can install the skill quickly without cloning the full RocketSim app repository.

The canonical install source for this skill is `https://github.com/AvdLee/RocketSim-Agent-Skill`, and the installable skill directory is `Agent-Skill/`.

## What It Does

- Detects the installed RocketSim version before interacting with the Simulator
- Uses the matching RocketSim CLI and bundled in-app skill for version-aware automation
- Lets agents list visible accessibility elements before taking action
- Enables agents to navigate screens, tap, long-press, swipe, type, and press simulator hardware buttons more reliably

## Why Install It

Install this skill if you want your AI coding tool to use RocketSim as its Simulator interaction layer instead of relying on ad-hoc automation.

This is especially useful if you want an agent to:

- Navigate repetitive Simulator flows faster with fewer retries
- Read visible UI state before interacting with the app
- Spend fewer tokens by using RocketSim's compact `--agent` element output
- Work against the RocketSim version already installed on your machine

## How It Works

The RocketSim Agent Skill stays intentionally small:

- `Agent-Skill/SKILL.md` discovers and validates a RocketSim app bundle
- It resolves the bundled in-app skill and CLI from the installed app
- The bundled skill contains the version-matched CLI guidance for simulator interaction

This split keeps the public discovery layer stable while letting the installed RocketSim app define the exact Simulator automation capabilities for that version.

## How to Use This Skill

Follow these steps:

1. Install [RocketSim from the Mac App Store](https://apps.apple.com/app/apple-store/id1504940162?pt=117264678&ct=Agent-Skill-Readme&mt=8).
2. Install this skill using one of the options below.
3. Open your agent and write a prompt similar to the example prompt shown below.

Example prompt:

> Use RocketSim to navigate through <your_app_name> in the Simulator

### Option A: Using skills.sh

Install this skill with a single command:

```bash
npx skills add https://github.com/AvdLee/RocketSim-Agent-Skill --skill rocketsim
```

### Option B: Claude Code Plugin

To install this skill for your personal use in Claude Code:

1. Add the marketplace:

```bash
/plugin marketplace add AvdLee/RocketSim-Agent-Skill
```

2. Install the skill:

```bash
/plugin install rocketsim@rocketsim-agent-skill
```

### Option C: Cursor

This repository includes Cursor plugin metadata. Install or symlink the `Agent-Skill/` folder into your Cursor skills directory following Cursor's documentation:

```bash
mkdir -p .cursor/skills
ln -s /path/to/RocketSim-Agent-Skill/Agent-Skill .cursor/skills/rocketsim
```

### Option D: Codex / OpenAI-compatible tools

This repository includes an `agents/openai.yaml` manifest. Copy or symlink the `Agent-Skill/` folder into your Codex skills directory:

```bash
cp -R Agent-Skill/ "$CODEX_HOME/skills/rocketsim"
```

See the [Codex skills documentation](https://developers.openai.com/codex/skills/) for details on where to save skills.

All packaged install surfaces in this repository intentionally point at this same repository and the `Agent-Skill/` directory, so keep those paths aligned if you fork or repackage the skill.

## How to Verify

Open an agent and ask it to:

> Use RocketSim to navigate through the `<app_name>` in the Simulator

If the skill is installed and RocketSim is running, the agent should detect the installed RocketSim version, connect through the bundled CLI, inspect the visible Simulator UI, and start interacting with the app based on what is on screen.

## See Also My Other Skills

- [SwiftUI Expert](https://github.com/AvdLee/SwiftUI-Agent-Skill)
- [Core Data Expert](https://github.com/AvdLee/Core-Data-Agent-Skill)
- [Swift Testing Expert](https://github.com/AvdLee/Swift-Testing-Agent-Skill)
- [Swift Concurrency Expert](https://github.com/AvdLee/Swift-Concurrency-Agent-Skill)
- [Xcode Build Optimization](https://github.com/AvdLee/Xcode-Build-Optimization-Agent-Skill)

## Learn More

If you want to learn more about RocketSim itself, use the canonical product resources:

- [RocketSim website](https://www.rocketsim.app)
- [RocketSim documentation](https://www.rocketsim.app/docs)
- [RocketSim on the Mac App Store](https://apps.apple.com/app/apple-store/id1504940162?pt=117264678&ct=Agent-Skill-Readme&mt=8)

## Support and Feedback

- Open a GitHub issue in this repository for skill-specific bugs or installation issues.
- Use the RocketSim documentation site for product guides and feature help.

RocketSim is developed by [Antoine van der Lee](https://www.avanderlee.com) and built by SwiftLee.
