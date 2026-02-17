# Pgg Universal Memory-bank - Public Install Guide

This public repository contains only installation instructions.

Core installer logic and templates are kept in a private repository and can be used only by approved collaborators.

## Access Requirements

1. You must be added as an approved collaborator/team member on the private installer repo.
2. You must authenticate locally:

```powershell
gh auth login
```

If you do not have access, install commands will fail with `403` or `404`.

## One-Command Install (Run in target project root)

### Backend

```powershell
$repo = "figchamdemb/Pgg-uni-memory-bank"
$tmp = Join-Path $env:TEMP "install-backend.ps1"
gh api -H "Accept: application/vnd.github.raw" "/repos/$repo/contents/install-backend.ps1?ref=main" > $tmp
powershell -ExecutionPolicy Bypass -File $tmp -TargetRepoPath (Get-Location).Path
```

### Frontend

```powershell
$repo = "figchamdemb/Pgg-uni-memory-bank"
$tmp = Join-Path $env:TEMP "install-frontend.ps1"
gh api -H "Accept: application/vnd.github.raw" "/repos/$repo/contents/install-frontend.ps1?ref=main" > $tmp
powershell -ExecutionPolicy Bypass -File $tmp -TargetRepoPath (Get-Location).Path
```

### Mobile

```powershell
$repo = "figchamdemb/Pgg-uni-memory-bank"
$tmp = Join-Path $env:TEMP "install-mobile.ps1"
gh api -H "Accept: application/vnd.github.raw" "/repos/$repo/contents/install-mobile.ps1?ref=main" > $tmp
powershell -ExecutionPolicy Bypass -File $tmp -TargetRepoPath (Get-Location).Path
```

## What Gets Installed Into Target Repo

- `Memory-bank/` structure and docs
- `AGENTS.md` start/end protocol
- local hook guard and CI workflow
- summary/generation scripts

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
