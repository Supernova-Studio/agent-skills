# Supernova Agent Skills

<p align="center">
  <a aria-label="Join the community on Slack" href="https://community.supernova.io"><img alt="" src="https://img.shields.io/badge/Join%20the%20community-black.svg?style=for-the-badge&logo=Slack"></a>
</p>

Agent Skills to help developers using AI agents with Supernova. Skills in this repo follow the [Agent Skills](https://agentskills.io/)
format.

## Installation

### Install all skills

```bash
npx skills add supernova-studio/agent-skills
```

### Install a specific skill

```bash
npx skills add supernova-studio/agent-skills --skill capture-frontend-feedback
```

## Available Skills

| Skill Name                                                     | Description                                                                                                                                                                                                             |
| -------------------------------------------------------------- | ----------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------------- |
| [capture-frontend-feedback](skills/capture-frontend-feedback/) | Capture design-system friction during frontend work (missing context, conflicts, reformulations, corrections) via the `sn_collect_agent_feedback` MCP tool. Requires feedback collection to be enabled in your context. |

## License

MIT
