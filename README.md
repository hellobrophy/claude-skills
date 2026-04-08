# Claude Skills

A collection of Claude Skills I use in every day work.

I've taken a lot of the below information on the basic skills and their use from [travisvn's](https://github.com/travisvn) excellent repo [awesome-claude-skills](https://github.com/travisvn/awesome-claude-skills/tree/main).

There's a whole bunch of skills related info, recommendations and tutorials on there that I haven't listed.

## Getting Started

### Claude.ai Web Interface

1. Go to [Settings > Capabilities](https://claude.ai/settings/capabilities)
2. Enable Skills toggle
3. Browse available skills or upload custom skills
4. **For Team/Enterprise**: Admin must enable Skills organization-wide first

### Claude Code CLI

```bash
# Install skills from marketplace
/plugin marketplace add anthropics/skills

# Or install from local directory
/plugin add /path/to/skill-directory
```

## The Basic Skills Set

The following are commonly understood to be the 'standard set' of skills everyone should have.

### Anthropc Development Skills

- **[frontend-design](https://github.com/anthropics/skills/blob/main/skills/frontend-design)** - Instructs Claude to avoid "AI slop" or generic aesthetics and to make bold design decisions. Works very well for React & Tailwind.
- **[web-artifacts-builder](https://github.com/anthropics/skills/tree/main/skills/web-artifacts-builder)** - Build complex claude.ai HTML artifacts using React, Tailwind CSS, and shadcn/ui components
- **[mcp-builder](https://github.com/anthropics/skills/tree/main/skills/mcp-builder)** - Guide for creating high-quality MCP servers to integrate external APIs and services
- **[webapp-testing](https://github.com/anthropics/skills/tree/main/skills/webapp-testing)** - Test local web applications using Playwright for UI verification and debugging

### Skill Creation

- **[skill-creator](https://github.com/anthropics/skills/tree/main/skills/skill-creator)** - Interactive skill creation tool that guides you through building new skills with Q&A

## Complimentary Skils to the above

The following are in this repo and are written to compliment the Basic Skills Set above.
