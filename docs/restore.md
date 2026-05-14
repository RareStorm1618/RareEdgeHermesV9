# Restore Checklist

Use this as a recovery guide for the RareEdge Hermes setup.

## 1. Install Or Update Hermes

Install Hermes normally, then verify:

```powershell
hermes --version
hermes doctor
```

Expected version at the time this setup was captured:

```text
Hermes Agent v0.13.0 (2026.5.7)
```

## 2. Restore Config Shape

Copy `config/config.redacted.yaml` to the Hermes home as a starting point:

```powershell
Copy-Item .\config\config.redacted.yaml "$env:LOCALAPPDATA\hermes\config.yaml"
```

Then replace every `<REDACTED>` value with the correct local secret or leave the field empty if it is unused.

Copy the persona file:

```powershell
Copy-Item .\config\SOUL.md "$env:LOCALAPPDATA\hermes\SOUL.md"
```

## 3. Restore Secrets Locally

Create or update:

```text
C:\Users\MP3-Backup\AppData\Local\hermes\.env
```

Do not commit `.env`.

At minimum, configure the provider credentials and Discord token used by the gateway.

## 4. Install Gateway Dependencies

For Discord text gateway support:

```powershell
$py = "$env:LOCALAPPDATA\hermes\hermes-agent\venv\Scripts\python.exe"
& $py -m pip install "discord.py[voice]==2.7.1" "aiohttp==3.13.3"
```

Opus is optional and only needed for Discord voice playback.

## 5. Migrate And Check

```powershell
hermes config migrate
hermes skills list
hermes doctor
```

## 6. Start Gateway

```powershell
hermes gateway install
hermes gateway start
hermes gateway status
```

Confirm the fresh log contains `discord connected`:

```powershell
Get-Content "$env:LOCALAPPDATA\hermes\logs\gateway.log" -Tail 80
```
