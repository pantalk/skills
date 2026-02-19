---
name: pantalk-send-message
description: Send a message to a chat channel or thread via any connected platform (Slack, Discord, Mattermost, Telegram, WhatsApp).
---

Use this skill when you need to send a message to a specific channel, thread, or user on any chat platform connected through pantalkd.

## Prerequisites

- `pantalkd` must be running with at least one bot configured.
- The target bot must be defined in your pantalk config.

## Send a Message

```bash
pantalk send --bot <bot-name> --channel <channel-id> --text "Your message here"
```

The daemon automatically resolves the platform from the bot's `type` - no need to specify it.

### Required Flags

| Flag                                    | Description                                |
| --------------------------------------- | ------------------------------------------ |
| `--bot`                                 | Bot name as defined in your pantalk config |
| `--text`                                | The message content to send                |
| `--channel` or `--target` or `--thread` | At least one destination identifier        |

### Reply to a Thread

```bash
pantalk send --bot ops-bot --channel C0123ABCD --thread 1700000000.000001 --text "Acknowledged, investigating now."
```

### Send to a Generic Target

```bash
pantalk send --bot support-bot --target 1234567890 --text "Ticket resolved. Closing thread."
```

## JSON Output

JSON output is automatic when stdout is not a terminal (e.g. when called by an agent). You can also force it:

```bash
pantalk send --bot ops-bot --channel C0123ABCD --text "Update deployed" --json
```

## When to Use

- Posting status updates to an ops channel
- Replying to a user question in a support thread
- Sending alerts or notifications across platforms
- Confirming task completion to a requesting user
