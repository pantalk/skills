---
name: pantalk-status
description: Check the health and runtime state of the pantalkd daemon — uptime, connected bots, and configured agent runners.
---

Use this skill to verify that `pantalkd` is running and healthy before sending messages or reading notifications. It returns uptime, a list of connected bots, and any configured agent definitions.

## Prerequisites

- `pantalkd` must be running.

## Check Daemon Status

```bash
pantalk status
```

### Example Output (human-readable)

```
uptime:  2h34m
started: 2025-10-01 09:00:00
bots:    2
  ops-bot               slack
  alerts-bot            telegram
agents:  1
  responder             when: direct || mentions
```

## JSON Output

JSON output is automatic when stdout is not a terminal (e.g. when called by an agent). You can also force it:

```bash
pantalk status --json
```

### Example JSON Response

```json
{
  "started_at": "2025-10-01T09:00:00Z",
  "uptime_sec": 9240,
  "bots": [
    { "name": "ops-bot", "service": "slack", "display_name": "Ops Bot" },
    { "name": "alerts-bot", "service": "telegram", "display_name": "Alerts Bot" }
  ],
  "agents": [
    { "name": "responder", "when": "direct || mentions" }
  ]
}
```

## Flags

| Flag       | Description                                             |
| ---------- | ------------------------------------------------------- |
| `--socket` | Path to the daemon socket (default: auto-detected)      |
| `--json`   | Force JSON output (automatic when stdout is not a TTY)  |

## When to Use

- Before sending a message, to confirm the target bot is connected.
- After starting `pantalkd`, to verify bots and agents loaded correctly.
- In health-check scripts that monitor whether the daemon is alive.
- To list active agent runners and their trigger conditions.

## Combining with Other Commands

```bash
# Confirm daemon is alive and a specific bot is connected
pantalk status --json | jq '.bots[] | select(.name == "ops-bot")'

# Check uptime before sending a message
UPTIME=$(pantalk status --json | jq '.uptime_sec')
if [ "$UPTIME" -gt 0 ]; then
  pantalk send --bot ops-bot --channel C0123ABCD --text "Deployment complete."
fi
```
