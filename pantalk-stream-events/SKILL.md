---
name: pantalk-stream-events
description: Stream real-time chat events from any platform for continuous agent monitoring and response.
---

Use this skill when your agent needs to continuously monitor chat activity in real time - reacting to messages, mentions, and events as they happen.

## Prerequisites

- `pantalkd` must be running with at least one bot configured.
- Streaming holds an open connection to the daemon's Unix socket.

## Start a Stream

```bash
pantalk stream --bot <bot-name>
```

This opens a persistent connection and prints each event as a JSON line as it arrives. JSON output is automatic when called by an agent (non-TTY stdout).

The stream **disconnects automatically after 60 seconds** by default to prevent agent tools from blocking indefinitely. Use `--timeout` to change or disable:

```bash
# Custom timeout (seconds)
pantalk stream --bot ops-bot --timeout 120

# No timeout (interactive / long-running use)
pantalk stream --bot ops-bot --timeout 0
```

### Filter the Stream

```bash
# Only notification-worthy events (mentions, DMs)
pantalk stream --bot ops-bot --notify

# Events from a specific channel
pantalk stream --bot ops-bot --channel C0123ABCD

# Events from a specific thread
pantalk stream --bot support-bot --thread 1234567890
```

## Processing Events

Each line of output is a JSON object with the same structure as notification events. Your agent can pipe the stream into a processing loop:

```bash
pantalk stream --bot ops-bot --notify | while read -r event; do
  echo "$event" | jq -r '.text'
done
```

### Key Event Fields

| Field             | Description                                    |
| ----------------- | ---------------------------------------------- |
| `kind`            | Event type (`message`)                         |
| `direction`       | `in` (inbound) or `out` (outbound)             |
| `user`            | Platform user ID of the sender                 |
| `self`            | `true` if the event was sent by the bot itself |
| `target`          | Normalized destination (e.g. `channel:C0123`)  |
| `text`            | Message content                                |
| `channel`         | Channel identifier                             |
| `thread`          | Thread identifier                              |
| `mentions_agent`  | Agent was @mentioned                           |
| `direct_to_agent` | Direct message to the agent                    |

## When to Use

- Running a long-lived agent that reacts to messages in real time
- Monitoring channels for keywords, alerts, or escalation triggers
- Building event-driven agent workflows
- Watching for @mentions to trigger on-demand agent tasks
