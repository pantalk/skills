<p align="center">
  <img src="https://pantalk.dev/icon.svg" alt="Pantalk" width="80" height="80" />
</p>

<h3 align="center">Pantalk Skills</h3>

<p align="center">
  Pre-defined skill definitions that describe Pantalk's capabilities in a format AI agents can understand.
</p>

---

## Overview

Each skill is a standalone `SKILL.md` file that documents a single Pantalk capability — what it does, how to invoke it, and when to use it. AI agents and tool-use frameworks can consume these files to understand what actions are available.

## Available Skills

| Skill                                             | Description                                                    |
| ------------------------------------------------- | -------------------------------------------------------------- |
| [send-message](send-message/SKILL.md)             | Send messages to any channel or thread across all platforms    |
| [read-notifications](read-notifications/SKILL.md) | Poll for mentions, DMs, and notification-worthy events         |
| [read-history](read-history/SKILL.md)             | Retrieve conversation history for context and reasoning        |
| [stream-events](stream-events/SKILL.md)           | Subscribe to real-time event streams for continuous monitoring |
| [list-bots](list-bots/SKILL.md)                   | Discover available bots and their platform connections         |
| [manage-config](manage-config/SKILL.md)           | Setup, modify, and validate pantalk configuration              |

## How Skills Work

Skills are not code — they're structured documentation. Each `SKILL.md` contains:

- **Frontmatter** with `name` and `description` for machine indexing
- **Prerequisites** the agent must satisfy before using the skill
- **Usage examples** with exact CLI commands
- **Flag/option tables** for structured parameter reference
- **When to Use** guidance for agent decision-making

An agent framework can read these files to populate its tool registry, or an LLM can use them as context to know which commands to run.

## Supported Platforms

All skills work identically across every platform — just swap the client binary:

| Platform   | Client               |
| ---------- | -------------------- |
| Slack      | `pantalk-slack`      |
| Discord    | `pantalk-discord`    |
| Mattermost | `pantalk-mattermost` |
| Telegram   | `pantalk-telegram`   |

---

<p align="center">
  <a href="https://pantalk.dev">pantalk.dev</a></sub>
</p>
