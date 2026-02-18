---
name: pantalk-list-bots
description: List all configured bots and their status across connected chat platforms.
---

Use this skill when your agent needs to discover which bots are available, what platforms they're connected to, and their identifiers.

## Prerequisites

- `pantalkd` must be running with at least one bot configured.

## List All Bots

```bash
pantalk bots
```

This returns all configured bots. JSON output is automatic when called by an agent (non-TTY stdout).

## Output Structure

Each bot entry contains:

| Field          | Description                                   |
| -------------- | --------------------------------------------- |
| `service`      | Bot type / platform (slack, discord, etc.)    |
| `name`         | Bot name as defined in config                 |
| `bot_id`       | Platform user ID (auto-discovered at runtime) |
| `display_name` | Human-readable bot display name               |

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
- Verifying that bots are connected and registered
- Building routing logic based on available bot identifiers
- Health-checking the pantalkd setup
