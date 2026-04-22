# Agent Guidelines for RocketSim Agent Skill

This document provides guidance for AI agents working with this repository so the skill stays focused, accurate, and easy to install.

## Core Principles

### 1. RocketSim Focus Only
This is a RocketSim skill repository. Keep the content focused on:
- simulator interaction through the `rocketsim` CLI on `PATH`
- the `rs/1` JSON protocol
- the agent loop (snapshot → act → wait → re-snapshot)
- packaging and release metadata for the skill

Do not add:
- general iOS app testing strategies
- architecture guidance for apps under test
- broad Swift or SwiftUI tutorials
- comparisons with competing tools

### 2. Keep The Skill Trigger-Ready
The most important lines in this repository are the YAML frontmatter in `Agent-Skill/SKILL.md` and the install metadata in the plugin manifests.

When updating them:
- prefer clear trigger phrases like RocketSim, Simulator, visible elements, tap, swipe, type, scroll, focus, navigate
- keep the skill description user-intent oriented
- avoid vague wording like "helps with simulator tasks"

### 3. Keep The Skill Token-Minimal
The whole point of this skill is to *reduce* tokens spent per agent session. Every change should ask: does this earn its tokens?

Do:
- favor short tables and one-line examples over prose
- keep error taxonomies as flat tables, not paragraphs
- link to <https://www.rocketsim.app/docs/features/agentic-development/> for long-form details rather than duplicating them (that page is authored in the [`AvdLee/RocketSimApp`](https://github.com/AvdLee/RocketSimApp) repo — open a docs PR there in the same release window when the CLI surface changes)

Do not:
- reintroduce a discovery flow (we removed that in 2.0; the CLI must be on `PATH`)
- duplicate the canonical `rs/1` protocol reference inside this repo
- add tutorial-style scaffolding when a code snippet would do

### 4. Packaging Files Must Stay In Sync
Whenever the skill changes in a way that affects installation or versioning, update the packaging files together:
- `agents/openai.yaml`
- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`
- `.cursor-plugin/plugin.json`

Keep repository URLs and skill paths aligned. Versions are bumped automatically by `.github/workflows/release.yml` on dispatch — do not bump them manually in PRs.

### 5. README Should Sell The Outcome
The README should explain why this skill matters:
- it is a companion to RocketSim
- it gives agents a stateful way to interact with the Simulator with low token cost
- it stays in lock-step with the installed RocketSim version automatically (because the CLI on `PATH` is a symlink into the app bundle)

Avoid over-explaining internal implementation details in the opening sections.

## Language And Tone

Use language that is:
- clear
- direct
- developer-focused
- factual without being dry

Prefer:
- "Use RocketSim to inspect visible UI and interact with the Simulator"
- "Talk to the `rocketsim` CLI on `PATH` directly via the `rs/1` protocol"

Avoid:
- heavy product hype
- competitor comparisons
- vague claims without concrete capabilities

## Updating This Repository

When making changes, check:
1. Is the skill still focused on the `rs/1` agent loop?
2. Does the SKILL.md still pay for its tokens?
3. Do the plugin manifests still reference the right repository and skill path?
4. Does the README still make the value obvious to first-time visitors?

## Summary

**Focus**: low-token, CLI-direct simulator interaction via the `rs/1` protocol
**Avoid**: discovery flows, generic app architecture, competitor comparisons, broad testing advice
**Goal**: make it easy for AI tools to install and use RocketSim as the best interaction layer for iOS Simulator automation
