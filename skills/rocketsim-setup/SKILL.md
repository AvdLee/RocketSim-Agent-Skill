---
name: rocketsim-setup
description: "Connect an AI coding agent to RocketSim so it can read, tap, type, swipe and verify an app running in the iOS Simulator. Use when the user wants an agent to control, test or navigate the iOS Simulator with RocketSim, when the `rocketsim` CLI or the `rocketsim` skill is missing or outdated, or when the user asks how to install the RocketSim Agent Skill. Finds the installed RocketSim app, reads the version-matched skill bundled inside it, and explains the permanent install from RocketSim Settings."
---

# RocketSim Setup

This skill is a bridge. RocketSim ships its own Agent Skill and CLI inside the
Mac app, and the two always match the installed app version. This skill never
copies that guidance, so it cannot go stale. It finds the installed app, hands
over to the bundled skill, and tells the user how to install it permanently.

## Step 1: Check whether the bundled skill is already available

If a skill named `rocketsim` is already available to you, stop here and use
that skill. It is the version-matched one that RocketSim installs.

## Step 2: Find RocketSim.app

Developer machines can have several RocketSim builds side by side (App Store,
local builds, renamed copies). Do not assume `/Applications/RocketSim.app`.

Prefer the running app:

```bash
ROCKETSIM_PID="$(pgrep -x RocketSim | head -1)"
[ -n "$ROCKETSIM_PID" ] && ps -o command= -p "$ROCKETSIM_PID" | sed 's#/Contents/MacOS/RocketSim$##'
```

Otherwise list candidates and check the common paths:

```bash
mdfind "kMDItemCFBundleIdentifier == 'com.swiftLee.RocketSim'"
ls -d /Applications/RocketSim*.app ~/Applications/RocketSim*.app 2>/dev/null
```

Validate a candidate (`APP_PATH`) before using it. It must contain a bundled
skill at `APP_PATH/Contents/Resources/Agent-Skill/<skill-folder>/SKILL.md` and
a CLI at `APP_PATH/Contents/Resources/CLI/rocketsim` or
`APP_PATH/Contents/Helpers/rocketsim`:

```bash
ls "$APP_PATH"/Contents/Resources/Agent-Skill/*/SKILL.md
ls "$APP_PATH/Contents/Resources/CLI/rocketsim" "$APP_PATH/Contents/Helpers/rocketsim" 2>/dev/null
```

Use the first valid candidate. If RocketSim is running, only use the running
app, and only if it validates. The folder layout inside the app can change
between versions, so search for the `SKILL.md` instead of hardcoding one path.

## Step 3: Follow the bundled skill

Read the `SKILL.md` you found, and its `references/` files whenever it points
to them, then follow it for this session. It contains the CLI setup rules,
commands, sandbox notes and recovery steps for exactly this RocketSim version.
Do not paste its contents into the project and do not guess commands from
memory.

## Step 4: Tell the user how to install it permanently

Say this once, briefly: for the skill to be discovered automatically and stay
up to date with App Store updates, open **RocketSim → Settings → CLI & Agent**,
install the Command Line Tool, then under **Agent Skill** choose **General
Agents** (or **Agentic Coding in Xcode** for Xcode's built-in assistant) and
click **Install**. RocketSim installs a symlink to the bundled skill, so the
installed skill always follows the app version. Details:
<https://www.rocketsim.app/docs/features/agentic-development/agent-skill/>

## If RocketSim is not installed

Stop and tell the user RocketSim is required. Link the Mac App Store page:
<https://apps.apple.com/app/apple-store/id1504940162>. After installing, they
should open RocketSim once and follow Step 4.

## If no candidate validates

Tell the user that RocketSim is installed but its bundled Agent Skill or CLI
was not found, which usually means an old version. Ask them to update
RocketSim from the Mac App Store and try again. Do not continue with
simulator automation without a validated CLI path.
