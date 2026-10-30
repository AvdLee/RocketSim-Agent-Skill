![RocketSim Agent Skill](assets/banner.jpg)

# RocketSim Agent Skill

The RocketSim Agent Skill and CLI let AI coding agents such as Claude Code,
Codex, Cursor and Xcode's built-in coding assistant read, tap, type, swipe and
verify an app running in the iOS Simulator. [RocketSim](https://www.rocketsim.app)
is a Mac app, and the skill and CLI ship inside it.

## How do I let an AI agent control and test the iOS Simulator?

1. Install [RocketSim from the Mac App Store](https://apps.apple.com/app/apple-store/id1504940162).
2. Open **RocketSim → Settings → CLI & Agent** and click **Install Command Line Tool**.
3. Under **Agent Skill**, choose **General Agents** (or **Agentic Coding in Xcode**) and click **Install**.
4. Restart your coding tool if it does not pick up the skill, then ask it:
   `Use RocketSim to navigate through <your app> in the Simulator`.

RocketSim installs the skill as a symlink to the version that is bundled inside
the app, so the skill and the `rocketsim` CLI always match the installed
version and stay current with App Store updates.

Full guide: [RocketSim Agent Skill docs](https://www.rocketsim.app/docs/features/agentic-development/agent-skill/).

## Install with `npx skills`

If you prefer to discover the skill through the skills ecosystem:

```bash
npx skills add avdlee/rocketsim-agent-skill
```

This installs `rocketsim-setup`, a small bridge skill. It does not contain
RocketSim's instructions. It finds the RocketSim app on your Mac, reads the
version-matched skill bundled inside it, follows it for the session, and tells
you how to do the permanent install from RocketSim Settings. Because it only
points at the bundled skill, it cannot go out of date. RocketSim is still
required.

## What can the agent do?

- Read visible accessibility elements as a compact snapshot
- Tap, long-press, swipe, scroll and type by label or coordinates
- Press Simulator hardware buttons and answer Face ID prompts
- Share a live, interactive browser preview of the Simulator
- Audit the visible screen for accessibility problems and verify the fixes
- Capture screenshots when visual context is needed

RocketSim does not build, install or launch apps from source. Use your IDE or a
build tool such as `xcodebuild` or XcodeBuildMCP for that, then use RocketSim
once the app is running in the Simulator.

## How does it compare with other Simulator tools?

In a July 2026 head-to-head benchmark, RocketSim used about 99% fewer tokens,
took up about 95% less of the agent's context and ran about 24% faster than
other tools for controlling the iOS Simulator. Read
[how the CLI and Agent Skill are tested](https://www.rocketsim.app/blog/testing-ai-agents-ios-simulator/)
for the scenarios, methodology and results.

## Why is this repository small?

An earlier version of this repository held a standalone copy of the skill. That
copy could drift from the CLI inside the app, so the skill now ships with
RocketSim itself. This repository only holds the bridge skill and this guide.

## Learn more

- [Agentic development with RocketSim](https://www.rocketsim.app/docs/features/agentic-development/)
- [RocketSim CLI](https://www.rocketsim.app/docs/features/agentic-development/rocketsim-cli/)
- [The rs/1 agent protocol](https://www.rocketsim.app/docs/features/agentic-development/rs1-protocol/)
- [CLI & Agent settings](https://www.rocketsim.app/docs/settings/cli-and-agent/)
- [AI-powered Xcode Simulator automation that is token-efficient](https://www.avanderlee.com/ai-development/ai-powered-xcode-simulator-automation-token-efficient/)
