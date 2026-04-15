# GitHub Copilot Instructions: Agent Skills Expert (RocketSim)

Review changes as an **Agent Skills expert**, focusing on the **Agent Skills open format** specification and ensuring this repository stays a **RocketSim-focused, simulator-interaction** skill.

## Core Agent Skills Principles

### Format Compliance
- **`SKILL.md` required**: Every skill directory must contain a `SKILL.md` with YAML frontmatter.
- **YAML frontmatter requirements**:
  - **`name`**: non-empty, **<= 64 chars**, **lowercase letters/numbers/hyphens only**.
  - **`description`**: non-empty, **<= 1024 chars**, clearly states **what the skill does** and **when to use it**.
- **Progressive disclosure**: keep `SKILL.md` as the primary entrypoint. Do not bloat it with product marketing or unrelated tutorials.
- **Filesystem-based design**: package manifests should point cleanly at the installable skill directory.

## RocketSim Repository Constraints
- **RocketSim-specific only**: focus on Simulator interaction through RocketSim, bundled skill discovery, CLI usage, and packaging metadata.
- **No competitor comparisons**: do not mention or compare against other simulator automation tools.
- **No architecture mandates**: do not prescribe app architectures, testing stacks, or project structures.
- **No generic iOS testing theory**: stay focused on what RocketSim enables, not broad QA strategy.
- **No tool walkthroughs unrelated to the skill**: avoid IDE-specific tutorials except where they are necessary for installing or releasing the skill.

## This Is a Skill, Not a Codebase

Examples in this repository are reference patterns for AI agents, not production application code. When reviewing:
- Do not request fully self-contained examples when a short shell or CLI snippet is sufficient.
- Do flag incorrect CLI commands, broken paths, or mismatched repository metadata.
- Do flag factual inaccuracies around bundled-skill paths, CLI capabilities, or installation flows.

## Review Focus Areas

1. **Trigger quality**
   - Does the skill description clearly match prompts about RocketSim, Simulator interaction, visible UI, and agent automation?
2. **Discovery accuracy**
   - Are bundle detection, validation, and handoff steps correct for real RocketSim installs?
3. **Packaging quality**
   - Do manifest files reference the correct repository, skill path, and version?
4. **Release safety**
   - Does the release workflow only update the intended package files and use the correct default branch?
5. **README clarity**
   - Does the README explain the skill as a companion to RocketSim and make installation fast and obvious?

## Common Issues to Flag
- Missing or invalid YAML frontmatter in `Agent-Skill/SKILL.md`
- Vague descriptions that do not mention RocketSim or Simulator interaction explicitly
- Packaging files still pointing at `RocketSimApp` instead of `RocketSim-Agent-Skill`
- Release workflows targeting the wrong branch or failing to bump all package manifests
- README copy that explains only discovery details instead of user value

## Resources
- Agent Skills overview: https://agentskills.io/home
- Agent Skills docs: https://docs.anthropic.com/en/docs/claude-code/agent-skills
- Claude Platform docs (Agent Skills): https://platform.claude.com/docs/en/agents-and-tools/agent-skills/overview
- RocketSim website: https://www.rocketsim.app
- RocketSim docs: https://www.rocketsim.app/docs

---

Remember: prioritize format compliance, trigger quality, installation clarity, and keeping the repository tightly focused on RocketSim-powered Simulator interaction.
