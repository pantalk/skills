---
name: send-message
description: Send a message to a chat channel or thread via any connected platform (Slack, Discord, Mattermost, Telegram).
---

Use this skill when you need to send a message to a specific channel, thread, or user on any chat platform connected through pantalkd.

## Prerequisites

- `pantalkd` must be running with at least one service configured.
- The target bot must be defined in `pantalk.yaml`.

## Send a Message

```bash
pantalk-slack send --bot <bot-name> --channel <channel-id> --text "Your message here"
```

Replace `pantalk-slack` with the appropriate service client (`pantalk-discord`, `pantalk-mattermost`, `pantalk-telegram`).

### Required Flags

| Flag                                    | Description                                       |
| --------------------------------------- | ------------------------------------------------- |
| `--bot`                                 | Bot name as defined in your `pantalk.yaml` config |
| `--text`                                | The message content to send                       |
| `--channel` or `--target` or `--thread` | At least one destination identifier               |

### Reply to a Thread

```bash
pantalk-slack send --bot ops-bot --channel C0123ABCD --thread 1700000000.000001 --text "Acknowledged, investigating now."
```

### Send to a Generic Target

```bash
pantalk-discord send --bot support-bot --target 1234567890 --text "Ticket resolved. Closing thread."
```

## JSON Output

All send commands confirm delivery with an event. Use `--json` for structured output your agent can parse:

```bash
pantalk-slack send --bot ops-bot --channel C0123ABCD --text "Update deployed" 2>/dev/null
```

## When to Use

- Posting status updates to an ops channel
- Replying to a user question in a support thread
- Sending alerts or notifications across platforms
- Confirming task completion to a requesting user
