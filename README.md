# Windframe Agent Skills

Agent skills for [Windframe](https://windframe.dev), the AI-powered web UI builder.

## Skills

| Skill | Description |
| ----- | ----------- |
| [windframe](./skills/windframe/) | Build, extend, and restyle web interfaces using live Windframe design context |

## Installation

Browse and install interactively:

```bash
npx skills add https://github.com/Devwares-Team/windframe-skill
```

Install Windframe directly:

```bash
npx skills add https://github.com/Devwares-Team/windframe-skill --skill windframe
```

Reload skills or restart the agent after installation. If your agent does not discover the skill, point it at the installed `SKILL.md`.

## Requirements

- A [Windframe](https://windframe.dev) account with Pro API access.
- An API key created on your [account page](https://app.windframe.dev/account), available to the agent as `WINDFRAME_API_KEY`. See [key setup](skills/windframe/references/authentication.md) for secure local configuration and future sessions. Never paste keys into chat or commit them.

## Usage

In agents that expose skills as slash commands:

```text
/windframe create a pricing page
/windframe redesign this screen
```
