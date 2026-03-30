---
name: hermes-agent-setup
description: Help users configure Hermes Agent — CLI usage, setup wizard, model/provider selection, tools, skills, voice/STT/TTS, gateway, and troubleshooting. Use when someone asks to enable features, configure settings, or needs help with Hermes itself.
version: 1.1.0
author: Hermes Agent
tags: [setup, configuration, tools, stt, tts, voice, hermes, cli, skills]
---

# Hermes Agent Setup & Configuration

Use this skill when a user asks about configuring Hermes, enabling features, setting up voice, managing tools/skills, or troubleshooting. **Example workflow for web data retrieval:**

1. **Handle 404 errors** by trying alternative URLs (e.g., weather.com → Weather Underground)
2. **Extract specific data** from browser snapshots using element references
3. **Verify results** by cross-checking multiple sources
4. **Document fallback strategies** for unreliable endpoints

**Key steps:**
- Use `browser_navigate` with error handling
- Extract data via `browser_snapshot`
- Validate results across sources
- Document alternative approaches in troubleshooting sections

## Key Paths

- Config: `~/.hermes/config.yaml`
- API keys: `~/.hermes/.env`
- Skills: `~/.hermes/skills/`
- Hermes install: `~/.hermes/hermes-agent/`
- Venv: `~/.hermes/hermes-agent/venv/`

## CLI Overview

Hermes is used via the `hermes` command (or `python -m hermes_cli.main` from the repo).

### Core commands:

```
hermes                          Interactive chat (default)
hermes chat -q "question"       Single query, then exit
hermes chat -m MODEL            Chat with a specific model
hermes -c                       Resume most recent session
hermes -c "project name"        Resume session by name
hermes --resume SESSION_ID      Resume by exact ID
hermes -w                       Isolated git worktree mode
hermes -s skill1,skill2         Preload skills for the session
hermes --yolo                   Skip dangerous command approval
```

### Configuration & setup:

```
hermes setup                    Interactive setup wizard (provider, API keys, model)
hermes model                    Interactive model/provider selection
hermes config                   View current configuration
hermes config edit              Open config.yaml in $EDITOR
hermes config set KEY VALUE     Set a config value directly
hermes login                    Authenticate with a provider
hermes logout                   Clear stored auth
hermes doctor                   Check configuration and dependencies
```

### Tools & skills:

```
hermes tools                    Interactive tool enable/disable per platform
hermes skills list              List installed skills
hermes skills search QUERY      Search the skills hub
hermes skills install NAME      Install a skill from the hub
hermes skills config            Enable/disable skills per platform
```

### Gateway (messaging platforms):

```
hermes gateway run              Start the messaging gateway
hermes gateway install          Install gateway as background service
hermes gateway status           Check gateway status
```

### Session management:

```
hermes sessions list            List past sessions
hermes sessions browse          Interactive session picker
hermes sessions rename ID TITLE Rename a session
hermes sessions export ID       Export session as markdown
hermes sessions prune           Clean up old sessions
```

### Other:

```
hermes status                   Show status of all components
hermes cron list                List cron jobs
hermes insights                 Usage analytics
hermes update                   Update to latest version
hermes pairing                  Manage DM authorization codes
```

## Setup Wizard (`hermes setup`)

The interactive setup wizard walks through:
1. **Provider selection** — OpenRouter, Anthropic, OpenAI, Google, DeepSeek, and many more
2. **API key entry** — stores securely in the env file
3. **Model selection** — picks from available models for the chosen provider
4. **Basic settings** — reasoning effort, tool preferences

Run it from terminal:
```bash
cd ~/.hermes/hermes-agent
source venv/bin/activate
python -m hermes_cli.main setup
```

To change just the model/provider later: `hermes model`

## Skills Configuration (`hermes skills`)

Skills are reusable instruction sets that extend what Hermes can do.

### Managing skills:

```bash
hermes skills list              # Show installed skills
hermes skills search "docker"   # Search the hub
hermes skills install NAME      # Install from hub
hermes skills config            # Enable/disable per platform
```

### Per-platform skill control:

`hermes skills config` opens an interactive UI where you can enable or disable specific skills for each platform (cli, telegram, discord, etc.). Disabled skills won't appear in the agent's available skills list for that platform.

### Loading skills in a session:

- CLI: `hermes -s skill-name` or `hermes -s skill1,skill2`
- Chat: `/skill skill-name`
- Gateway: type `/skill skill-name` in any chat

## Voice Messages (STT)

Voice messages from Telegram/Discord/WhatsApp/Slack/Signal are auto-transcribed when an STT provider is available.

### Provider priority (auto-detected):
1. **Local faster-whisper** — free, no API key, runs on CPU/GPU
2. **Groq Whisper** — free tier, needs GROQ_API_KEY
3. **OpenAI Whisper** — paid, needs VOICE_TOOLS_OPENAI_KEY

### Setup local STT (recommended):

```bash
cd ~/.hermes/hermes-agent
source venv/bin/activate
pip install faster-whisper
```

Add to config.yaml under the `stt:` section:
```yaml
stt:
  enabled: true
  provider: local
  local:
    model: base  # Options: tiny, base, small, medium, large-v3
```

Model downloads automatically on first use (~150 MB for base).

### Setup Groq STT (free cloud):

1. Get free key from https://console.groq.com
2. Add GROQ_API_KEY to the env file
3. Set provider to groq in config.yaml stt section

### Verify STT:

After config changes, restart the gateway (send /restart in chat, or restart `hermes gateway run`). Then send a voice message.

## Voice Replies (TTS)

Hermes can reply with voice when users send voice messages.

### TTS providers (set API key in env file):

| Provider | Env var | Free? |
|----------|---------|-------|
| ElevenLabs | ELEVENLABS_API_KEY | Free tier |
| OpenAI | VOICE_TOOLS_OPENAI_KEY | Paid |
| Kokoro (local) | None needed | Free |
| Fish Audio | FISH_AUDIO_API_KEY | Free tier |

### Voice commands (in any chat):
- `/voice on` — voice reply to voice messages only
- `/voice tts` — voice reply to all messages
- `/voice off` — text only (default)

## Enabling/Disabling Tools (`hermes tools`)

### Interactive tool config:

```bash
cd ~/.hermes/hermes-agent
source venv/bin/activate
python -m hermes_cli.main tools
```

This opens a curses UI to enable/disable toolsets per platform (cli, telegram, discord, slack, etc.).

### After changing tools:

Use `/reset` in the chat to start a fresh session with the new toolset. Tool changes do NOT take effect mid-conversation (this preserves prompt caching and avoids cost spikes).

### Common toolsets:

| Toolset | What it provides |
|---------|-----------------|
| terminal | Shell command execution |
| file | File read/write/search/patch |
| web | Web search and extraction |
| browser | Browser automation (needs Browserbase) |
| image_gen | AI image generation |
| mcp | MCP server connections |
| voice | Text-to-speech output |
| cronjob | Scheduled tasks |

## Installing Dependencies

Some tools need extra packages:

```bash
cd ~/.hermes/hermes-agent && source venv/bin/activate

pip install faster-whisper    # Local STT (voice transcription)
pip install browserbase       # Browser automation
pip install mcp               # MCP server connections
```

## Config File Reference

The main config file is `~/.hermes/config.yaml`. Key sections:

```yaml
# Model and provider
model:
  default: anthropic/claude-opus-4.6
  provider: openrouter

# Agent behavior
agent:
  max_turns: 90
  reasoning_effort: high    # xhigh, high, medium, low, minimal, none

# Voice
stt:
  enabled: true
  provider: local           # local, groq, openai
tts:
  provider: elevenlabs      # elevenlabs, openai, kokoro, fish

# Display
display:
  skin: default             # default, ares, mono, slate
  tool_progress: full       # full, compact, off
  background_process_notifications: all  # all, result, error, off
```

Edit with `hermes config edit` or `hermes config set KEY VALUE`.

**Pitfall:** There is no `hermes config get` command. To view config, use `hermes config show`. The valid subcommands are: `show`, `edit`, `set`, `path`, `env-path`, `check`, `migrate`.

**Pitfall:** The `model:` section can end up with both `default` and `name` keys (e.g. `model.default: anthropic/claude-opus-4.6` and `model.name: openrouter/llama-3.1-70b-instruct`). These may conflict — flag this to the user if both are present.

## Gateway Commands (Messaging Platforms)

| Command | What it does |
|---------|-------------|
| /reset or /new | Fresh session (picks up new tool config) |
| /help | Show all commands |
| /model [name] | Show or change model |
| /compact | Compress conversation to save context |
| /voice [mode] | Configure voice replies |
| /reasoning [effort] | Set reasoning level |
| /sethome | Set home channel for cron/notifications |
| /restart | Restart the gateway (picks up config changes) |
| /status | Show session info |
| /retry | Retry last message |
| /undo | Remove last exchange |
| /personality [name] | Set agent personality |
| /skill [name] | Load a skill |

## Setting Up Messaging Platforms

### Important notes
- `hermes setup messaging` does NOT exist. Valid setup sections: `model`, `terminal`, `gateway`, `tools`, `agent`
- The interactive setup wizard requires a TTY — it cannot run from within an agent session. Do platform setup manually when running as an agent.
- The gateway is a **user-level systemd service**. Always use `systemctl --user` (never `sudo systemctl`):
  ```bash
  systemctl --user restart hermes-gateway.service
  systemctl --user status hermes-gateway.service --no-pager
  journalctl --user -u hermes-gateway.service -n 50 --no-pager
  ```

### Discord setup

1. **Create bot** at https://discord.com/developers/applications
   - New Application → Bot tab → Reset Token → copy it
   - Enable Privileged Gateway Intents: Message Content Intent ✅
   - OAuth2 → URL Generator: scope `bot`, permissions: Send Messages, Read Message History, Attach Files, Embed Links
   - Use generated URL to invite bot to server

2. **Add token and allowed users to ~/.hermes/.env:**
   ```
   DISCORD_BOT_TOKEN=your_token_here
   DISCORD_ALLOWED_USER_IDS=your_discord_user_id
   ```

3. **Enable full toolset in ~/.hermes/config.yaml** under `platform_toolsets:`
   Replace `- hermes-discord` with the full toolset list (match telegram's toolset).

4. **Discord-specific config** (in config.yaml):
   ```yaml
   discord:
     require_mention: true        # bot only responds when @mentioned
     free_response_channels: ''   # comma-separated channel IDs for auto-response
     auto_thread: true            # replies go into threads
   ```

5. **Restart gateway:**
   ```bash
   systemctl --user restart hermes-gateway.service
   ```

### Telegram setup

1. Create bot via @BotFather on Telegram, get the token
2. Add to ~/.hermes/.env:
   ```
   TELEGRAM_BOT_TOKEN=your_token_here
   TELEGRAM_ALLOWED_USER_IDS=your_telegram_user_id
   ```
3. Enable full toolset under `platform_toolsets: telegram:` in config.yaml
4. Restart gateway

### Slack setup

1. Create a Slack app at https://api.slack.com/apps
2. Add to ~/.hermes/.env:
   ```
   SLACK_APP_TOKEN=xapp-...
   SLACK_BOT_TOKEN=xoxb-...
   ```
3. Enable full toolset under `platform_toolsets: slack:` in config.yaml
4. Restart gateway

### General pattern for any platform
1. Add platform token/credentials to `~/.hermes/.env`
2. Add `PLATFORM_ALLOWED_USER_IDS=id1,id2` to restrict access
3. Update `platform_toolsets:` in config.yaml (replace `- hermes-platformname` with full toolset list)
4. Restart gateway: `systemctl --user restart hermes-gateway.service`

## Troubleshooting

### Voice messages not working
1. Check stt.enabled is true in config.yaml
2. Check a provider is available (faster-whisper installed, or API key set)
3. Restart gateway after config changes (/restart)

### Tool not available
1. Run `hermes tools` to check if the toolset is enabled for your platform
2. Some tools need env vars — check the env file
3. Use /reset after enabling tools

### Model/provider issues
1. Run `hermes doctor` to check configuration
2. Run `hermes login` to re-authenticate
3. Check the env file has the right API key

### Changes not taking effect
- Gateway: /reset for tool changes, /restart for config changes
- CLI: start a new session

### Skills not showing up
1. Check `hermes skills list` shows the skill
2. Check `hermes skills config` has it enabled for your platform
3. Load explicitly with `/skill name` or `hermes -s name`
