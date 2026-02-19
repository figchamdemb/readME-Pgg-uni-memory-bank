# Pgg Universal Memory-bank - Public Install Guide

This public repository contains only installation instructions.

Core installer logic and templates are kept in a private repository and can be used only by approved collaborators.

## Access Requirements

1. You must be added as an approved collaborator/team member on the private installer repo.
2. You must authenticate locally with the same GitHub account that was granted access:

```powershell
gh auth login --web --git-protocol https --hostname github.com
```

If you do not have access, install commands will fail with `403` or `404`.

### Owner setup (required once)

Private repo owner must:
1. Open private repo `Settings` -> `Collaborators and teams`.
2. Invite each developer account that should install the kit.
3. Developer must accept the invitation before running commands.

Quick access check:

```powershell
$gh = "C:\Program Files\GitHub CLI\gh.exe"
& $gh api /repos/figchamdemb/Pgg-uni-memory-bank --jq '.private'
```

Expected: `true` (no error).

## Step 0 (Recommended): PATH-Safe Bootstrap for VS Code

Run this once in the terminal before install commands:

```powershell
$gh = (Get-Command gh -ErrorAction SilentlyContinue).Source
if (-not $gh) {
  $gh = "C:\Program Files\GitHub CLI\gh.exe"
}
if (-not (Test-Path $gh)) {
  winget install --id GitHub.cli -e
  Write-Host "Restart VS Code terminal, then run this bootstrap again."
  return
}
& $gh --version
& $gh auth status
```

If `auth status` says not logged in:

```powershell
& $gh auth login --web --git-protocol https --hostname github.com
```

## One-Command Install (Run in target project root)

### Backend

```powershell
$gh = (Get-Command gh -ErrorAction SilentlyContinue).Source
if (-not $gh) { $gh = "C:\Program Files\GitHub CLI\gh.exe" }
$repo = "figchamdemb/Pgg-uni-memory-bank"
$tmp = Join-Path $env:TEMP "install-backend.ps1"
& $gh api -H "Accept: application/vnd.github.raw" "/repos/$repo/contents/install-backend.ps1?ref=main" > $tmp
powershell -ExecutionPolicy Bypass -File $tmp -TargetRepoPath (Get-Location).Path
```

### Frontend

```powershell
$gh = (Get-Command gh -ErrorAction SilentlyContinue).Source
if (-not $gh) { $gh = "C:\Program Files\GitHub CLI\gh.exe" }
$repo = "figchamdemb/Pgg-uni-memory-bank"
$tmp = Join-Path $env:TEMP "install-frontend.ps1"
& $gh api -H "Accept: application/vnd.github.raw" "/repos/$repo/contents/install-frontend.ps1?ref=main" > $tmp
powershell -ExecutionPolicy Bypass -File $tmp -TargetRepoPath (Get-Location).Path
```

### Mobile

```powershell
$gh = (Get-Command gh -ErrorAction SilentlyContinue).Source
if (-not $gh) { $gh = "C:\Program Files\GitHub CLI\gh.exe" }
$repo = "figchamdemb/Pgg-uni-memory-bank"
$tmp = Join-Path $env:TEMP "install-mobile.ps1"
& $gh api -H "Accept: application/vnd.github.raw" "/repos/$repo/contents/install-mobile.ps1?ref=main" > $tmp
powershell -ExecutionPolicy Bypass -File $tmp -TargetRepoPath (Get-Location).Path
```

## Start Session (Required Before Coding)

After install, run inside target repo:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\start_memory_bank_session.ps1
```

Non-interactive:

```powershell
powershell -ExecutionPolicy Bypass -File .\scripts\start_memory_bank_session.ps1 -Yes
```

This refreshes memory docs and writes:
- `Memory-bank/_generated/session-state.json`

Guard expects an active session and prompts refresh after session budget is used.

## Troubleshooting

- `gh : The term 'gh' is not recognized`
  - use the PATH-safe bootstrap block above
  - or fully close/reopen VS Code after GitHub CLI install
- `HTTP 403` or `HTTP 404` from `gh api`
  - your current GitHub account does not have access to the private repo
  - verify active account:
    - `& $gh auth status`
  - if wrong account, switch:
    - `& $gh auth logout`
    - `& $gh auth login --web --git-protocol https --hostname github.com`
- `WARNING: Target is not inside a git work tree; skipped hook installation.`
  - this means Memory-bank files were created, but hooks were not installed
  - fix:
    - `git init`
    - `powershell -ExecutionPolicy Bypass -File .\scripts\install_memory_bank_hooks.ps1 -Mode warn`
  - verify:
    - `git config --get core.hooksPath` -> `.githooks` or `<subfolder>/.githooks` in monorepos
    - `git config --get memorybank.mode` -> `warn` or `strict`

## What Gets Installed Into Target Repo

- `Memory-bank/` structure and docs
- `AGENTS.md` start/end protocol
- local hook guard and CI workflow
- summary/generation scripts
- session bootstrap scripts

Default enforcement mode is `warn`.  
Switch later when stable:

```powershell
git config memorybank.mode strict
```

## Security / IP Notes

- This repo is public by design for discoverability.
- Installer internals stay in private repo access control.
- Authorized users can still inspect local outputs/scripts they run.
- Do not store secrets in Memory-bank docs or scripts.
