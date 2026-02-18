---
name: pantalk-read-notifications
description: Read unseen notifications, mentions, and DMs across all connected chat platforms.
---

Use this skill when you need to check what messages require your agent's attention - mentions, direct messages, and other notification-worthy events across all connected platforms.

## Prerequisites

- `pantalkd` must be running with at least one bot configured.
- Notification history is stored in SQLite and persists across restarts.

## Fetch Unseen Notifications

```bash
pantalk notifications --bot <bot-name> --unseen
```

This returns only unseen notification events (mentions, DMs, and route-aware messages). JSON output is automatic when called by an agent (non-TTY stdout).

### Filter by Scope

```bash
# Notifications from a specific channel
pantalk notifications --bot ops-bot --channel C0123ABCD --unseen

# Notifications from a specific thread
pantalk notifications --bot ops-bot --thread 1700000000.000001 --unseen

# Search notification text (case-insensitive)
pantalk notifications --bot ops-bot --search "urgent" --unseen

# All notifications (not just unseen)
pantalk notifications --bot ops-bot --limit 50
```

### Poll for New Events

Use `--since <event-id>` to only retrieve events newer than a known checkpoint:

```bash
pantalk notifications --bot ops-bot --unseen --since 42
```

## Clear Notifications

After processing notifications, clear them so they don't appear again:

```bash
# Clear all unseen for a bot
pantalk notifications --bot ops-bot --unseen --clear

# Clear all unseen in a channel
pantalk notifications --bot ops-bot --channel C0123ABCD --unseen --clear

# Clear everything (use with caution)
pantalk notifications --clear --all
```

## Event Structure

Each notification event contains:

| Field             | Description                                      |
| ----------------- | ------------------------------------------------ |
| `id`              | Unique event ID (use for `--since` polling)      |
| `notification_id` | Notification-specific ID                         |
| `timestamp`       | Event timestamp (ISO 8601)                       |
| `service`         | Bot type / platform (slack, discord, etc.)       |
| `bot`             | Bot name from config                             |
| `kind`            | Event type (`message`)                           |
| `direction`       | `in` (inbound) or `out` (outbound)               |
| `user`            | Platform user ID of the sender                   |
| `self`            | `true` if the event was sent by the bot itself   |
| `target`          | Normalized destination (e.g. `channel:C0123`)    |
| `channel`         | Channel identifier                               |
| `thread`          | Thread identifier (if threaded)                  |
| `text`            | Message content                                  |
| `mentions_agent`  | Whether the agent was explicitly mentioned       |
| `direct_to_agent` | Whether this is a direct message                 |
| `notify`          | Whether the event triggered a notification       |
| `seen`            | Whether the notification has been marked as seen |

## When to Use

- Periodically polling for new messages that need agent attention
- Triaging inbound requests across multiple platforms
- Building an inbox-zero workflow for your agent
- Checking for urgent mentions before taking other actions
