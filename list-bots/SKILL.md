---
name: list-bots
description: List all configured bots and their status across connected chat platforms.
---

Use this skill when your agent needs to discover which bots are available, what platforms they're connected to, and their identifiers.

## Prerequisites

- `pantalkd` must be running with at least one service configured.

## List All Bots

```bash
pantalk-slack bots --json
```

This returns all bots registered for the specified service. To check bots across all services:

```bash
pantalk-slack bots --json
pantalk-discord bots --json
pantalk-mattermost bots --json
pantalk-telegram bots --json
```

## Output Structure

Each bot entry contains:

| Field          | Description                          |
| -------------- | ------------------------------------ |
| `service`      | Platform name (slack, discord, etc.) |
| `name`         | Bot name as defined in config        |
| `bot_id`       | Platform-specific bot identifier     |
| `display_name` | Human-readable bot display name      |

## Example Output

```json
[
  {
    "service": "slack",
    "name": "ops-bot",
    "bot_id": "U0123ABCDEF",
    "display_name": "Ops Agent"
  },
  {
    "service": "slack",
    "name": "support-bot",
    "bot_id": "U0456GHIJKL",
    "display_name": "Support Agent"
  }
]
```

## When to Use

- Discovering available bots before sending a message
- Verifying that a service is connected and bots are registered
- Building routing logic based on available bot identifiers
- Health-checking the pantalkd setup
