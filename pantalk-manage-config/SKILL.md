---
name: pantalk-manage-config
description: Manage pantalk configuration - add bots, validate settings, and hot-reload the daemon.
---

Use this skill when your agent needs to set up, modify, or validate the pantalk configuration for connecting to chat platforms.

## Prerequisites

- `pantalk` must be installed.
- A config file at `~/.config/pantalk/config.yaml` (can be generated with `setup`).

## Interactive Setup (Human Only)

Generate a new config interactively (writes to `~/.config/pantalk/config.yaml` by default). This command requires human interaction and cannot be used by AI agents:

```bash
pantalk setup
```

## Validate Config

Check that a config file is valid before starting the daemon:

```bash
pantalk validate
```

## View Current Config

```bash
pantalk config print
```

## Modify Config

### Set Server Options

```bash
pantalk config set-server --history 500
```

### Add a Bot

```bash
pantalk config add-bot --type slack --name ops-bot --bot-token '$SLACK_BOT_TOKEN' --app-level-token '$SLACK_APP_LEVEL_TOKEN'
```

> **Slack reminder:** Adding a Slack bot to the config is not enough on its own. Your Slack app must also have Socket Mode enabled, event subscriptions configured (`app_mention`, `message.channels`), and the correct bot scopes (`chat:write`, `channels:history`, `app_mentions:read`). See the pantalk-install skill for full setup steps.

### Remove a Bot

```bash
pantalk config remove-bot --name ops-bot
```

## Hot Reload

After modifying config, reload the running daemon without restarting:

```bash
pantalk reload
```

## When to Use

- Initial setup of pantalk for a new environment
- Adding a new chat platform or bot to an existing deployment
- Validating config changes before deploying
- Dynamically reconfiguring the daemon at runtime
