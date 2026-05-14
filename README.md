# RareEdge Hermes V9 Setup

Portable setup notes and sanitized configuration for the RareEdge Hermes Agent gateway.

This repository is for setup recovery, not for storing live secrets or the full Hermes source tree. The active machine setup currently lives at:

```text
C:\Users\MP3-Backup\AppData\Local\hermes
```

## Contents

- `config/config.redacted.yaml` - current Hermes config with secret-like values replaced by `<REDACTED>`.
- `config/SOUL.md` - current Hermes persona file.
- `docs/setup-status.md` - known-good status from the May 14, 2026 repair pass.
- `docs/restore.md` - recovery checklist for rebuilding the setup on this machine or another Windows machine.

## What Is Intentionally Not Stored

- API keys and provider tokens from `.env`.
- OAuth/auth state from `auth.json`.
- Logs, sessions, caches, lock files, pid files, and databases.
- The full `hermes-agent` install repo, which remains upstream at `NousResearch/hermes-agent`.

## Current Upstream

```text
git@github.com:NousResearch/hermes-agent.git
Hermes Agent v0.13.0 (2026.5.7)
```
