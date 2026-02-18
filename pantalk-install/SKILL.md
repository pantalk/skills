---
name: pantalk-install
description: Install, configure, and start the Pantalk daemon so your agent can communicate across Slack, Discord, Mattermost, and Telegram.
---

Use this skill to install Pantalk from pre-built binaries, create a configuration file, and start the daemon. After completing these steps your agent will have access to all Pantalk skills (send messages, read history, stream events, etc.).

## Step 1 - Download Pantalk Binaries

Download the latest release from GitHub and install the binaries. The script auto-detects your OS and architecture:

```bash
set -e

# Detect OS and architecture
OS="$(uname -s | tr '[:upper:]' '[:lower:]')"
ARCH="$(uname -m)"
case "${ARCH}" in
  x86_64)  ARCH="amd64" ;;
  aarch64|arm64) ARCH="arm64" ;;
esac

# Fetch latest release tag
VERSION="$(curl -fsSL https://api.github.com/repos/pantalk/pantalk/releases/latest | grep '"tag_name"' | sed -E 's/.*"([^"]+)".*/\1/')"

# Download and extract
ARCHIVE="pantalk-${VERSION}-${OS}-${ARCH}.tar.gz"
curl -fsSL "https://github.com/pantalk/pantalk/releases/download/${VERSION}/${ARCHIVE}" -o "/tmp/${ARCHIVE}"
tar -xzf "/tmp/${ARCHIVE}" -C /tmp

# Install binaries
INSTALL_DIR="${HOME}/.local/bin"
mkdir -p "${INSTALL_DIR}"
cp "/tmp/pantalk-${VERSION}-${OS}-${ARCH}/pantalk" "${INSTALL_DIR}/pantalk"
cp "/tmp/pantalk-${VERSION}-${OS}-${ARCH}/pantalkd" "${INSTALL_DIR}/pantalkd"
chmod +x "${INSTALL_DIR}/pantalk" "${INSTALL_DIR}/pantalkd"

# Clean up
rm -rf "/tmp/${ARCHIVE}" "/tmp/pantalk-${VERSION}-${OS}-${ARCH}"

echo "Installed pantalk ${VERSION} to ${INSTALL_DIR}"
```

## Step 2 - Verify Installation

Make sure the binaries are on your `PATH`:

```bash
export PATH="${HOME}/.local/bin:${PATH}"
pantalk --version
```

> **Note:** The `pantalk` binary handles both messaging commands and admin commands (setup, validate, reload, config). Add `export PATH="${HOME}/.local/bin:${PATH}"` to your shell profile to make it permanent.

## Step 3 - Create Configuration

Create the config file manually. Here is a minimal example with one Slack bot:

```yaml
# ~/.config/pantalk/config.yaml
server:
  notification_history_size: 1000

bots:
  - name: my-bot
    type: slack
    bot_token: $SLACK_BOT_TOKEN
    app_level_token: $SLACK_APP_LEVEL_TOKEN
    channels:
      - C0123456789
```

Write the file to `~/.config/pantalk/config.yaml`:

```bash
mkdir -p ~/.config/pantalk
cat > ~/.config/pantalk/config.yaml << 'EOF'
server:
  notification_history_size: 1000

bots:
  - name: my-bot
    type: slack
    bot_token: $SLACK_BOT_TOKEN
    app_level_token: $SLACK_APP_LEVEL_TOKEN
    channels:
      - C0123456789
EOF
```

> **Note:** `socket_path` and `db_path` have sensible XDG defaults (`$XDG_RUNTIME_DIR/pantalk.sock` and `~/.local/share/pantalk/pantalk.db`). You only need to set them if you want custom locations.

Add more bots (discord, mattermost, telegram) as needed. Each bot specifies its own `type`. See the full example config at `configs/pantalk.example.yaml`.

### Platform Setup Guides

Each platform requires its own app/bot setup before Pantalk can connect. See the detailed guides:

- [Slack Setup](https://github.com/pantalk/pantalk/blob/master/docs/slack-setup.md) — Create a Slack app with Socket Mode, scopes, and event subscriptions
- [Discord Setup](https://github.com/pantalk/pantalk/blob/master/docs/discord-setup.md) — Create a Discord application with Gateway intents
- [Mattermost Setup](https://github.com/pantalk/pantalk/blob/master/docs/mattermost-setup.md) — Create a bot account with endpoint configuration
- [Telegram Setup](https://github.com/pantalk/pantalk/blob/master/docs/telegram-setup.md) — Create a bot via BotFather

### Required Environment Variables

Set the tokens for each bot type you configure:

| Platform   | Variables                                  |
| ---------- | ------------------------------------------ |
| Slack      | `SLACK_BOT_TOKEN`, `SLACK_APP_LEVEL_TOKEN` |
| Discord    | `DISCORD_BOT_TOKEN`                        |
| Mattermost | `MATTERMOST_BOT_TOKEN`                     |
| Telegram   | `TELEGRAM_BOT_TOKEN`                       |

## Step 4 - Validate Configuration

```bash
pantalk validate
```

## Step 5 - Start the Daemon

The daemon runs in the **foreground** by default, so you must background it:

```bash
pantalkd &
```

Or redirect output to a log file:

```bash
pantalkd > /tmp/pantalkd.log 2>&1 &
```

The daemon loads config from `~/.config/pantalk/config.yaml` by default (override with `--config`). It listens on the Unix socket at `$XDG_RUNTIME_DIR/pantalk.sock`.

## Step 6 - Verify

List available bots to confirm the daemon is running and bots are connected:

```bash
pantalk bots
```

Send a test message:

```bash
pantalk send --bot my-bot --channel C0123456789 --text "Hello from Pantalk!"
```

## Step 7 - Install Skills

Install the Pantalk skill files so your agent knows how to use each capability:

```bash
pantalk skill install
```

This clones the skills repository and copies skill definitions into the appropriate agent directories (e.g. `.github/skills/`, `.cursor/skills/`, `.claude/skills/`). Use `--scope user` to install into home-level directories, or `--agents github,cursor` to target specific agents.

To update skills later:

```bash
pantalk skill update
```

## Next Steps

Once the daemon is running and skills are installed, your agent can use:

- **pantalk-send-message** - Send messages to any channel or thread
- **pantalk-read-notifications** - Poll for mentions and DMs
- **pantalk-read-history** - Read conversation history for context
- **pantalk-stream-events** - Subscribe to real-time event streams
- **pantalk-list-bots** - Discover available bots and platforms
- **pantalk-manage-config** - Modify and reload configuration at runtime

## When to Use

- Setting up Pantalk for the first time in a new environment
- Onboarding an AI agent with chat capabilities
- Re-installing or upgrading Pantalk binaries
- Bootstrapping a development or production deployment
