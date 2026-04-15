# Contributing to RocketSim Agent Skill

Thanks for your interest in improving this skill. Contributions that make RocketSim guidance clearer, more accurate, and easier to install are welcome.

## About Agent Skills
Agent Skills are structured prompt assets with:
- A `SKILL.md` file that defines behavior and workflows
- Packaging files that allow tools like Claude Code, Cursor, and OpenAI-compatible agents to install the skill

## What This Repository Covers
This repository is focused on the RocketSim Agent Skill only.

Good contributions include:
- improving trigger quality in `Agent-Skill/SKILL.md`
- fixing installation or packaging metadata
- improving release automation for the skill repository
- clarifying README copy and verification steps

## Quality Standards
- Keep content RocketSim-specific
- Keep the skill description explicit about when to use RocketSim
- Avoid competitor comparisons
- Avoid generic iOS architecture or testing guidance
- Keep packaging manifests and versions in sync

## Packaging Sync Checklist
Treat the following files as one surface area when changing install metadata, repository URLs, skill paths, or release versions:

- `Agent-Skill/SKILL.md`
- `agents/openai.yaml`
- `.claude-plugin/plugin.json`
- `.claude-plugin/marketplace.json`
- `.cursor-plugin/plugin.json`
- `README.md`

The canonical public repository for install commands and manifests is `https://github.com/AvdLee/RocketSim-Agent-Skill`, and the installable skill directory is `Agent-Skill/`.

## Pull Request Process
1. Create a focused branch.
2. Make the smallest change that fully solves the issue.
3. Ensure `Agent-Skill/SKILL.md`, the packaging manifests, and the README stay consistent.
4. Open a PR with a short summary and verification notes.

## Licensing Note
This repository is distributed under the proprietary terms in `LICENSE`. Maintainers may ask contributors to clarify authorship or rights before merging changes that affect distributable skill assets.

## Development Setup
- Standard Markdown and JSON editing is sufficient.
- Release versioning is handled by `.github/workflows/release.yml`.

## Resources
- Agent Skills documentation: https://docs.anthropic.com/en/docs/claude-code/agent-skills
- RocketSim website: https://www.rocketsim.app
- RocketSim docs: https://www.rocketsim.app/docs

## Code of Conduct
Be respectful and constructive. Assume positive intent and focus on improving the quality of the skill.
