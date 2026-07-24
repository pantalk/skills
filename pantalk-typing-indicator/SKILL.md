---
name: pantalk-typing-indicator
description: Show a "bot is typing..." indicator in a channel while the agent prepares a reply.
---

Use this skill when your agent is about to do slow work (reasoning, tool calls, file generation) before replying, so the humans in the channel see activity instead of dead air.

## Prerequisites

- `pantalkd` must be running with at least one bot configured.
- The target platform must support typing indicators. Currently: **Telegram**. Other platforms return `does not support typing indicators yet`.

## Start Typing

```bash
pantalk typing --bot <bot-name> --channel <channel-id>
```

One call is enough. The daemon keeps the indicator alive by re-pulsing the platform every few seconds until:

1. your bot **sends a message to the same destination** (the normal case - just send your reply), or
2. you stop it explicitly, or
3. a 60-second safety timeout expires (call `typing` again to renew if you are still working).

Do **not** re-invoke `typing` on a cadence yourself - the daemon owns the rhythm.

## Typical Agent Flow

```bash
# Human asked a question; agent starts thinking
pantalk typing --bot ops-bot --channel -1001234567890

# ...do the slow work: reasoning, tool calls, report generation...

# Sending the reply stops the indicator automatically
pantalk send --bot ops-bot --channel -1001234567890 --text "Here is the summary..."
```

## Stop Without Sending

If you decide not to reply after all:

```bash
pantalk typing --bot ops-bot --channel -1001234567890 --stop
```

## Threads

Pass `--thread` alongside `--channel` to scope the indicator to a forum topic or thread where the platform supports it:

```bash
pantalk typing --bot ops-bot --channel -1001234567890 --thread 42
```

## When to Use

- Immediately after reading a notification you intend to answer
- Before long tool invocations whose output feeds a chat reply
- Any time the gap between "message seen" and "reply sent" exceeds a few seconds
