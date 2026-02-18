---
name: manage-config
description: Manage pantalk configuration — add services, configure bots, and validate settings.
---

Use this skill when your agent needs to set up, modify, or validate the pantalk configuration for connecting to chat platforms.

## Prerequisites

- `pantalkctl` must be installed.
- A base `pantalk.yaml` config file (can be generated with `setup`).

## Interactive Setup

Generate a new config interactively:

```bash
pantalkctl setup --output ./configs/pantalk.yaml
```

## Validate Config

Check that a config file is valid before starting the daemon:

```bash
pantalkctl validate --config ./configs/pantalk.yaml
```

## View Current Config

```bash
pantalkctl config print --config ./configs/pantalk.yaml
```

## Modify Config

### Set Server Options

```bash
pantalkctl config set-server --socket /tmp/pantalk.sock --db ./data/pantalk.db --history 500
```

### Add a Service

```bash
pantalkctl config add-service --name slack --type slack
```

### Remove a Service

```bash
pantalkctl config remove-service --name slack
```

### Add a Bot to a Service

```bash
pantalkctl config add-bot --service slack --name ops-bot --token xoxb-...
```

### Remove a Bot

```bash
pantalkctl config remove-bot --service slack --name ops-bot
```

## Hot Reload

After modifying config, reload the running daemon without restarting:

```bash
pantalkctl reload
```

## When to Use

- Initial setup of pantalk for a new environment
- Adding a new chat platform or bot to an existing deployment
- Validating config changes before deploying
- Dynamically reconfiguring the daemon at runtime
