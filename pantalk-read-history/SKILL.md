---
name: pantalk-read-history
description: Read conversation history from any channel or thread to build context for agent reasoning.
---

Use this skill when your agent needs to read previous messages in a channel or thread to understand context before responding or taking action.

## Prerequisites

- `pantalkd` must be running with at least one bot configured.
- History is stored in SQLite and persists across process restarts.

## Fetch Channel History

```bash
pantalk history --bot <bot-name> --channel <channel-id>
```

JSON output is automatic when called by an agent (non-TTY stdout).

### Common Options

| Flag        | Description                                             |
| ----------- | ------------------------------------------------------- |
| `--bot`     | Bot name from config (optional, filters by bot)         |
| `--channel` | Channel to read history from                            |
| `--thread`  | Thread to read history from                             |
| `--target`  | Generic target filter                                   |
| `--search`  | Filter messages containing this text (case-insensitive) |
| `--limit`   | Number of events to return (default: 20)                |
| `--since`   | Only return events with ID greater than this value      |
| `--notify`  | Only return notification-worthy events                  |
| `--clear`   | Delete matching events instead of listing them          |
| `--all`     | Allow broad clear across all bots/channels              |
| `--json`    | Force JSON output (auto when non-TTY)                   |

## Attachments in History

Messages that carried files include an `attachments` array in JSON output, with a local `path` where the daemon stored the bytes:

```json
{"text": "see the graph", "attachments": [{"name": "latency.png", "mime": "image/png", "size": 20481, "path": "/home/user/.local/share/pantalk/media/9f/9f86d0...png"}]}
```

Read the file at `path` directly (e.g. to inspect an image or parse a document). An empty `path` means the bytes were not stored - only metadata was recorded.

A message that arrived with files but no text shows a synthetic placeholder as its `text`, e.g. `[attachment: photo.jpg]`. This placeholder is generated at query time - it is how you spot file-only messages in a listing, and it is not text the sender typed.

## Examples

### Read Recent Channel Messages

```bash
pantalk history --bot ops-bot --channel C0123ABCD --limit 30
```

### Read a Full Thread

```bash
pantalk history --bot support-bot --thread 1234567890 --limit 100
```

### Incremental Reads

Track the last seen event ID and only fetch new messages:

```bash
pantalk history --bot ops-bot --channel C0123ABCD --since 150
```

### Search Messages

Filter history to messages containing specific text (case-insensitive):

```bash
pantalk history --bot ops-bot --search "deploy" --limit 50
```

### Notification-Only History

Filter to only events that are relevant to the agent (mentions, DMs):

```bash
pantalk history --bot ops-bot --notify
```

## When to Use

- Reading conversation context before composing a reply
- Summarizing a thread for escalation or handoff
- Checking if a question was already answered
- Building memory for multi-turn agent conversations
