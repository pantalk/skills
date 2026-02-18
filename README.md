<p align="center">
  <img src="https://pantalk.dev/icon.svg" alt="Pantalk" width="80" height="80" />
</p>

<h3 align="center">Pantalk Skills</h3>

<p align="center">
  Pre-defined skill definitions that describe Pantalk's capabilities in a format AI agents can understand.
</p>

---

## Overview

Each skill is a standalone `SKILL.md` file that documents a single Pantalk capability - what it does, how to invoke it, and when to use it. AI agents and tool-use frameworks can consume these files to understand what actions are available.

## Available Skills

| Skill                                                             | Description                                                    |
| ----------------------------------------------------------------- | -------------------------------------------------------------- |
| [pantalk-install](pantalk-install/SKILL.md)                       | Install, configure, and start the Pantalk daemon               |
| [pantalk-send-message](pantalk-send-message/SKILL.md)             | Send messages to any channel or thread across all platforms    |
| [pantalk-read-notifications](pantalk-read-notifications/SKILL.md) | Poll for mentions, DMs, and notification-worthy events         |
| [pantalk-read-history](pantalk-read-history/SKILL.md)             | Retrieve conversation history for context and reasoning        |
| [pantalk-stream-events](pantalk-stream-events/SKILL.md)           | Subscribe to real-time event streams for continuous monitoring |
| [pantalk-list-bots](pantalk-list-bots/SKILL.md)                   | Discover available bots and their platform connections         |
| [pantalk-manage-config](pantalk-manage-config/SKILL.md)           | Setup, modify, and validate pantalk configuration              |

## How Skills Work

Skills are not code - they're structured documentation. Each `SKILL.md` contains:

- **Frontmatter** with `name` and `description` for machine indexing
- **Prerequisites** the agent must satisfy before using the skill
- **Usage examples** with exact CLI commands
- **Flag/option tables** for structured parameter reference
- **When to Use** guidance for agent decision-making

An agent framework can read these files to populate its tool registry, or an LLM can use them as context to know which commands to run.

## Unified CLI

All skills use the unified `pantalk` binary. The daemon resolves the platform from each bot's `type` automatically:

```bash
pantalk bots                              # list all configured bots
pantalk send --bot ops-bot --channel C0123 --text "hello"
pantalk notifications --bot ops-bot --unseen
pantalk stream --bot ops-bot --notify
```

---

<p align="center">
  <a href="https://pantalk.dev">pantalk.dev</a></sub>
</p>
