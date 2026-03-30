Hermes gateway runs as a systemd user service (not system-level). Always use `systemctl --user` commands, no sudo. Service name: hermes-gateway.service. Linger enabled.
§
Hermes gateway is a systemd user service (`systemctl --user restart hermes-gateway.service`). Config at ~/.hermes/config.yaml. Discord bot set up with full toolset, auto_thread=false, require_mention=true. Telegram also active with full toolset. `hermes setup` subcommands won't work from agent sessions (no TTY) — use `hermes config set` instead.
§
Hermes gateway uses both DISCORD_ALLOWED_USER_IDS and DISCORD_ALLOWED_USERS in .env; both are set to 887541301036343357. libopus0 is confirmed installed on the VPS.
§
Fixed STT configuration error: local provider requires valid whisper model names (base, small, medium, etc.) not "whisper-1" (OpenAI API name). Edit ~/.hermes/hiroute.yaml, change stt.model to a local model, then restart hermes-gateway.service.
§
User asked about "lln" — unclear if referring to LLM (Large Language Model), local model name, or another context. Need clarification to provide
§
User is setting up a fork of hermes-agent with WebAPI support from https://github.com/outsourc-e/hermes-agent.git. Note: 'hermes setup' requires TTY/interactive input which may fail in non-interactive terminal sessions; use 'hermes config set' for headless configuration.
§
User attempted to use non-existent tool 'fred_mortgage30us' for FRED mortgage rate data. No such tool available in current toolset. User insists on using this specific tool name for mortgage data retrieval.