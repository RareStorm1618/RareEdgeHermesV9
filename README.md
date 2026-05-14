# RareEdge Hermes V9 Backup

Automated backup of selected Hermes Agent local state.

Backed up nightly by Hermes. Secret-bearing and high-churn files are intentionally excluded, including `.env`, auth files, caches, logs, sessions, sandboxes, and lock/WAL files.

Included areas currently target:

- `skills/`
- `memories/`
- `hooks/`
- `cron/` metadata/scripts, minus outputs and caches
- `hermes-agent/` working files, minus nested git/caches/dependencies
- selected non-secret top-level state files
