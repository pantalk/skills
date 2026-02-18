---
name: read-notifications
description: Read unseen notifications, mentions, and DMs across all connected chat platforms.
---

Use this skill when you need to check what messages require your agent's attention — mentions, direct messages, and other notification-worthy events across all connected platforms.

## Prerequisites

- `pantalkd` must be running with at least one service configured.
- Notification history is stored in SQLite and persists across restarts.

## Fetch Unseen Notifications

```bash
pantalk-slack notifications --bot <bot-name> --unseen --json
```

This returns only unseen notification events (mentions, DMs, and route-aware messages) as a JSON array.

### Filter by Scope

```bash
# Notifications from a specific channel
pantalk-slack notifications --bot ops-bot --channel C0123ABCD --unseen --json

# Notifications from a specific thread
pantalk-slack notifications --bot ops-bot --thread 1700000000.000001 --unseen --json

# All notifications (not just unseen)
pantalk-slack notifications --bot ops-bot --limit 50 --json
```

### Poll for New Events

Use `--since <event-id>` to only retrieve events newer than a known checkpoint:

```bash
pantalk-slack notifications --bot ops-bot --unseen --since 42 --json
```

## Mark Notifications as Seen

After processing notifications, clear them so they don't appear again:

```bash
# Clear a single notification by ID
pantalk-slack clear-notifications --id 42

# Clear all unseen in a channel
pantalk-slack clear-notifications --bot ops-bot --channel C0123ABCD --unseen

# Clear everything (use with caution)
pantalk-slack clear-notifications --all
```

## Event Structure

Each notification event contains:

| Field             | Description                                        |
| ----------------- | -------------------------------------------------- |
| `id`              | Unique event ID (use for `--since` polling)        |
| `notification_id` | Notification-specific ID (use for `--id` clearing) |
| `service`         | Platform name (slack, discord, etc.)               |
| `bot`             | Bot name from config                               |
| `text`            | Message content                                    |
| `channel`         | Channel identifier                                 |
| `thread`          | Thread identifier (if threaded)                    |
| `mentions_agent`  | Whether the agent was explicitly mentioned         |
| `direct_to_agent` | Whether this is a direct message                   |
| `seen`            | Whether the notification has been marked as seen   |

## When to Use

- Periodically polling for new messages that need agent attention
- Triaging inbound requests across multiple platforms
- Building an inbox-zero workflow for your agent
- Checking for urgent mentions before taking other actions
