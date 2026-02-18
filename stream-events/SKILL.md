---
name: stream-events
description: Stream real-time chat events from any platform for continuous agent monitoring and response.
---

Use this skill when your agent needs to continuously monitor chat activity in real time — reacting to messages, mentions, and events as they happen.

## Prerequisites

- `pantalkd` must be running with at least one service configured.
- Streaming holds an open connection to the daemon's Unix socket.

## Start a Stream

```bash
pantalk-slack stream --bot <bot-name> --json
```

This opens a persistent connection and prints each event as a JSON line as it arrives.

### Filter the Stream

```bash
# Only notification-worthy events (mentions, DMs)
pantalk-slack stream --bot ops-bot --notify --json

# Events from a specific channel
pantalk-slack stream --bot ops-bot --channel C0123ABCD --json

# Events from a specific thread
pantalk-discord stream --bot support-bot --thread 1234567890 --json
```

## Processing Events

Each line of output is a JSON object with the same structure as notification events. Your agent can pipe the stream into a processing loop:

```bash
pantalk-slack stream --bot ops-bot --notify --json | while read -r event; do
  echo "$event" | jq -r '.text'
done
```

### Key Event Fields

| Field             | Description                 |
| ----------------- | --------------------------- |
| `kind`            | Event type (message, etc.)  |
| `direction`       | `inbound` or `outbound`     |
| `text`            | Message content             |
| `channel`         | Channel identifier          |
| `thread`          | Thread identifier           |
| `mentions_agent`  | Agent was @mentioned        |
| `direct_to_agent` | Direct message to the agent |

## When to Use

- Running a long-lived agent that reacts to messages in real time
- Monitoring channels for keywords, alerts, or escalation triggers
- Building event-driven agent workflows
- Watching for @mentions to trigger on-demand agent tasks
