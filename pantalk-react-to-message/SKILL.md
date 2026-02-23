---
name: pantalk-react-to-message
description: Add an emoji reaction to a chat message via any connected platform (Slack, Discord).
---

Use this skill when your agent needs to acknowledge a message with an emoji reaction — a lightweight alternative to sending a full reply. Reactions are ideal for signalling acknowledgement, approval, or status without flooding a channel with text responses.

## Prerequisites

- `pantalkd` must be running with at least one bot configured.
- **Slack**: the bot must have the `reactions:write` scope.
- **Discord**: the bot must have the `Add Reactions` permission in the target channel.

## Add a Reaction

```bash
pantalk react --bot <bot-name> --emoji <emoji> --channel <channel-id> --thread <message-ts>
```

JSON output is automatic when called by an agent (non-TTY stdout). The command prints `reacted` on success.

### Platform-Specific Requirements

| Platform | Required flags            | Emoji format                                    |
| -------- | ------------------------- | ----------------------------------------------- |
| Slack    | `--channel`, `--thread`   | Name without colons: `white_check_mark`, `eyes` |
| Discord  | `--channel`, `--target`   | Unicode character `✅` or `name:id` for custom  |

On Slack the `--thread` flag holds the message timestamp (e.g. `1700000000.123456`), which is the same value as the `thread` field in history/notification events.

On Discord the `--target` flag holds the message ID (snowflake), and `--channel` is the channel ID.

## Slack Examples

```bash
# Acknowledge a message with a check mark
pantalk react --bot ops-bot --channel C0123ABCD --thread 1700000000.123456 --emoji white_check_mark

# Add eyes emoji to show you are looking into it
pantalk react --bot ops-bot --channel C0123ABCD --thread 1700000000.123456 --emoji eyes

# React using the event data from notifications
EVENT=$(pantalk notifications --bot ops-bot --unseen --limit 1 --json | jq -r '.[0]')
CHANNEL=$(echo "$EVENT" | jq -r '.channel')
THREAD=$(echo "$EVENT" | jq -r '.thread // .timestamp' | sed 's/T.*//')
pantalk react --bot ops-bot --channel "$CHANNEL" --thread "$THREAD" --emoji white_check_mark
```

## Discord Examples

```bash
# React with a thumbs-up (unicode)
pantalk react --bot ops-bot --channel 987654321012345678 --target 1234567890123456789 --emoji 👍

# React with a custom server emoji
pantalk react --bot ops-bot --channel 987654321012345678 --target 1234567890123456789 --emoji "approved:1234567890"
```

## Reacting to Incoming Events

A common agent pattern is to react to every notification to signal it was received, then process it:

```bash
pantalk notifications --bot ops-bot --unseen --json | jq -c '.[]' | while read -r event; do
  channel=$(echo "$event" | jq -r '.channel')
  thread=$(echo "$event" | jq -r '.thread')

  # Acknowledge with a reaction immediately
  pantalk react --bot ops-bot --channel "$channel" --thread "$thread" --emoji eyes

  # Process the event and reply
  reply=$(echo "$event" | jq -r '.text' | your-agent-command)
  pantalk send --bot ops-bot --channel "$channel" --thread "$thread" --text "$reply"

  # Mark done
  pantalk react --bot ops-bot --channel "$channel" --thread "$thread" --emoji white_check_mark
done
```

## Key Fields from Notification Events

Use these fields from `pantalk notifications --json` output to build the react command:

| Event field | React flag  | Notes                                  |
| ----------- | ----------- | -------------------------------------- |
| `channel`   | `--channel` | Channel ID (all platforms)             |
| `thread`    | `--thread`  | Message timestamp (Slack)              |
| `target`    | `--target`  | Message ID (Discord, generic)          |

## Error Handling

If the reaction fails (e.g. bot lacks permission, emoji does not exist), the command exits with code 1 and prints the error to stderr:

```bash
if ! pantalk react --bot ops-bot --channel C0123ABCD --thread 1700000000.123456 --emoji white_check_mark; then
  echo "reaction failed, falling back to reply" >&2
  pantalk send --bot ops-bot --channel C0123ABCD --thread 1700000000.123456 --text "✅ Acknowledged"
fi
```

## When to Use

- Acknowledging messages without adding noise to a channel
- Signalling that your agent is processing a request (`eyes` reaction)
- Marking completion of a task (`white_check_mark`, `done`)
- Voting or polling workflows (multiple reactions on one message)
- Building lightweight approval flows where a thumbs-up means proceed
