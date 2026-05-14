# Setup Status

Last checked: 2026-05-14

## Working State

- Hermes Agent is installed at `C:\Users\MP3-Backup\AppData\Local\hermes\hermes-agent`.
- CLI reports `Hermes Agent v0.13.0 (2026.5.7)`.
- Config migrated to version `v23`.
- `hermes doctor` passes core checks.
- Discord gateway starts and connects successfully.
- Gateway is installed as a Windows login startup command.
- Skills Hub directory is initialized.
- Required Python packages are installed, including OpenAI SDK, Rich, dotenv, PyYAML, HTTPX, Croniter, `discord.py`, and `aiohttp`.

## Fixes Applied

- Updated Hermes from the stale active package to v0.13.0.
- Repaired the Windows launcher path and UTF-8 environment variables.
- Migrated config from v14 to v23.
- Removed invalid optional provider environment variables from the user environment.
- Initialized Skills Hub with `hermes skills list`.
- Installed missing Discord gateway dependency `aiohttp==3.13.3`.
- Installed Discord voice extras dependencies `PyNaCl` and `davey`; Opus codec is still optional and not installed.
- Restarted the gateway and verified Discord connection in logs.

## Current Expected Warnings

These are not blockers for Discord text operation:

- Optional providers are not logged in: Nous Portal, Gemini OAuth, MiniMax OAuth.
- `GITHUB_TOKEN` is not configured, so unauthenticated GitHub API rate limits are lower.
- Optional submodule `tinker-atropos` is missing.
- Optional toolsets that require extra API keys or system dependencies remain unavailable.
- Opus codec is missing, so Discord voice playback is disabled.

## Gateway Paths

Login startup command:

```text
C:\Users\MP3-Backup\AppData\Roaming\Microsoft\Windows\Start Menu\Programs\Startup\Hermes_Gateway.cmd
```

Service command:

```text
C:\Users\MP3-Backup\AppData\Local\hermes\gateway-service\Hermes_Gateway.cmd
```

Logs:

```text
C:\Users\MP3-Backup\AppData\Local\hermes\logs\gateway.log
C:\Users\MP3-Backup\AppData\Local\hermes\logs\errors.log
```
