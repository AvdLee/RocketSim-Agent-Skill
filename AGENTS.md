# Agent Guidelines for RocketSim Agent Skill

This document provides guidance for AI agents working with this repository so the skill stays focused, accurate, and easy to install.

## Core Principles

### 1. RocketSim Focus Only
This is a RocketSim skill repository. Keep the content focused on:
- simulator interaction through RocketSim
- the public discovery skill
- the bundled in-app skill
- the RocketSim CLI
- packaging and release metadata for the skill

Do not add:
- general iOS app testing strategies
- architecture guidance for apps under test
- broad Swift or SwiftUI tutorials
- comparisons with competing tools

### 2. Keep The Skill Trigger-Ready
The most important lines in this repository are the YAML frontmatter in `Agent-Skill/SKILL.md` and the install metadata in the plugin manifests.

When updating them:
- prefer clear trigger phrases like RocketSim, Simulator, visible elements, tap, swipe, type, and navigate
- keep the skill description user-intent oriented
- avoid vague wording like "helps with simulator tasks"

### 3. Keep Discovery Accurate
The discovery flow must reflect real-world RocketSim installs.

Do:
- prefer the running RocketSim app when available
- validate the bundled skill and CLI paths before proceeding
- document failure modes clearly

Do not:
- assume `/Applications/RocketSim.app` is always correct
- allow the skill to continue without a validated CLI path

### 4. Packaging Files Must Stay In Sync
Whenever the skill changes in a way that affects installation or versioning, update the packaging files together:
- `agents/openai.yaml`
- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`
- `.cursor-plugin/plugin.json`

Keep repository URLs, version numbers, and skill paths aligned.

### 5. README Should Sell The Outcome
The README should explain why this skill matters:
- it is a companion to RocketSim
- it gives agents a stateful way to interact with the Simulator
- it reduces guesswork and token waste by using RocketSim as the interaction layer

Avoid over-explaining internal implementation details in the opening sections.

## Language And Tone

Use language that is:
- clear
- direct
- developer-focused
- factual without being dry

Prefer:
- "Use RocketSim to inspect visible UI and interact with the Simulator"
- "Detects the installed RocketSim version and uses the matching CLI"

Avoid:
- heavy product hype
- competitor comparisons
- vague claims without concrete capabilities

## Updating This Repository

When making changes, check:
1. Is the skill still focused on RocketSim-powered Simulator interaction?
2. Is the discovery flow still accurate?
3. Do the plugin manifests still reference the right repository and skill path?
4. Does the README still make the value obvious to first-time visitors?

## Summary

**Focus**: RocketSim-driven Simulator interaction
**Avoid**: generic app architecture, competitor comparisons, broad testing advice
**Goal**: make it easy for AI tools to install and use RocketSim as the best interaction layer for iOS Simulator automation
