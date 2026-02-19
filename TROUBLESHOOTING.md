# Troubleshooting Guide

If setup fails, use this list.

## Quick Health Check
Run:

```powershell
pg version
```

## Error: `pg : The term 'pg' is not recognized`
Meaning:
- Global `pg` is not installed in this terminal session.

Fix:
1. Run machine setup again from `NON_TECHNICAL_QUICKSTART.md`.
2. Close and reopen terminal.
3. Verify with `pg version`.

Temporary current-shell fix:

```powershell
$env:Path += ";$HOME\.pg-cli"
pg version
```

## Error: `gh : The term 'gh' is not recognized`
Meaning:
- GitHub CLI is missing.

Fix:

```powershell
winget install --id GitHub.cli -e
```

Then restart terminal.

## Error: `Target path not found: (Get-Location).Path`
Meaning:
- You ran PowerShell syntax in CMD.

Fix:
- CMD:

```bat
pg install backend --target "%CD%"
```

- PowerShell:

```powershell
pg install backend --target (Get-Location).Path
```

- Cross-shell safe:

```bat
pg install backend --target .
```

## Error: `Cannot validate argument on parameter 'Command' ... "install"...`
Meaning:
- Old local `scripts/pg.ps1` is being used.

Fix:

```powershell
powershell -ExecutionPolicy Bypass -File "$HOME\.pg-cli\pg.ps1" install backend --target .
```

Then run `pg install backend --target .` again.

## Error: `gh: Not Found (HTTP 404)`
Meaning:
- Wrong repo/file path, or you do not have private repo access.

Fix:
1. Confirm private repo access.
2. Check logged-in account:

```powershell
gh auth status
```

3. Ask owner to add your account and accept invitation.

## Error: HTTP 403 / Permission denied
Meaning:
- Logged in account is not authorized.

Fix:
1. Confirm owner granted access.
2. Re-login:

```powershell
gh auth logout -h github.com
gh auth login --web --git-protocol https --hostname github.com
```

## Error: PowerShell shows `>>`
Meaning:
- Incomplete command (continuation mode).

Fix:
1. Press `Ctrl + C`.
2. Run command again as one full line.

## Warning: `Target is not inside a git work tree; skipped hook installation.`
Meaning:
- Files were created but hooks were not installed.

Fix:

```powershell
git init
powershell -ExecutionPolicy Bypass -File .\scripts\install_memory_bank_hooks.ps1 -Mode warn
```

Verify:

```powershell
git config --get core.hooksPath
```

## Command Order Reminder
- Machine once: install global `pg`.
- Repo once: `pg install backend|frontend|mobile`.
- Daily: `pg start`, `pg status`, `pg end`.
