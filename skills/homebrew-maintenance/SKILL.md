---
name: Homebrew Maintenance
description: Use when user asks about "Homebrew cleanup", "brew cleanup", "outdated packages", "Homebrew cache", "brew autoremove", "remove unused brew packages", or needs guidance on managing Homebrew installations on macOS.
version: 0.1.0
allowed-tools: Bash
---

# Homebrew Maintenance

Managing Homebrew packages and keeping the installation clean and efficient.

## Understanding Homebrew Storage

### Where Homebrew Stores Data

| Location | Purpose |
|----------|---------|
| `/opt/homebrew/` (Apple Silicon) | Installed packages |
| `/usr/local/` (Intel) | Installed packages |
| `$(brew --cache)` | Downloaded files |
| `/opt/homebrew/Caskroom/` | Installed casks (apps) |

### Check Installation Size

```bash
du -sh $(brew --prefix)
du -sh $(brew --cache)
```

## Regular Maintenance Commands

### Update Homebrew

```bash
brew update
```

Updates Homebrew itself and all tap information.

### Upgrade Packages

```bash
# Upgrade all
brew upgrade

# Upgrade specific
brew upgrade package_name
```

### Check Outdated

```bash
brew outdated
```

### Cleanup

```bash
# Remove old versions
brew cleanup

# See what would be removed
brew cleanup -n

# Also remove cache
brew cleanup -s
```

## Cleanup Deep Dive

### What `brew cleanup` Removes

- Old versions of installed packages
- Downloads older than 120 days
- Stale lock files
- Old package downloads

### Automatic Cleanup

Homebrew runs `brew cleanup` automatically every 30 days and after upgrades.

To disable:
```bash
export HOMEBREW_NO_INSTALL_CLEANUP=1
```

### Manual Cleanup Options

```bash
# Basic cleanup
brew cleanup

# Remove older than X days
brew cleanup --prune=30

# Also scrub cache
brew cleanup -s

# Dry run
brew cleanup -n
```

## Managing Dependencies

### Find Orphaned Packages

```bash
# Show packages that were only installed as dependencies
brew autoremove --dry-run
```

### Remove Orphaned Packages

```bash
brew autoremove
```

### Understanding Dependencies

```bash
# Show why a package is installed
brew uses --installed package_name

# Show dependencies of a package
brew deps package_name

# Show all dependents (reverse deps)
brew uses package_name
```

### List Top-Level Packages Only

```bash
brew leaves
```

These are packages you explicitly installed, not dependencies.

## Cache Management

### Cache Location

```bash
brew --cache
```

Usually `~/Library/Caches/Homebrew/`.

### Cache Size

```bash
du -sh $(brew --cache)
```

### Clear Cache Completely

```bash
rm -rf $(brew --cache)
```

## Package Management

### List Installed

```bash
# All packages (formulae)
brew list

# Only casks (GUI apps)
brew list --cask

# With versions
brew list --versions
```

### Remove Package

```bash
# Remove formula
brew uninstall package_name

# Remove cask
brew uninstall --cask app_name
```

### Package Info

```bash
brew info package_name
```

## Health Check

### Diagnose Issues

```bash
brew doctor
```

Fix any issues reported.

### Check for Issues

```bash
# Missing dependencies
brew missing

# Outdated casks
brew outdated --cask
```

## Cask Management

### List Installed Casks

```bash
brew list --cask
```

### Upgrade Casks

```bash
# Upgrade all casks
brew upgrade --cask

# Upgrade specific
brew upgrade --cask app_name
```

### Outdated Casks

```bash
brew outdated --cask
```

### Cask Cleanup

```bash
# Remove old cask versions
brew cleanup --cask
```

## Services

### List Services

```bash
brew services list
```

### Stop Unused Services

```bash
brew services stop service_name
```

### Remove Service

```bash
brew services cleanup
```

## Tap Management

### List Taps

```bash
brew tap
```

### Remove Unused Tap

```bash
brew untap tap_name
```

## Best Practices

### Regular Maintenance Schedule

| Frequency | Task |
|-----------|------|
| Weekly | `brew update && brew upgrade` |
| Monthly | `brew cleanup && brew autoremove` |
| Quarterly | `brew doctor` |

### Before Major Changes

```bash
# Create bundle file (backup)
brew bundle dump --file=~/Brewfile

# Restore later
brew bundle install --file=~/Brewfile
```

### Keep It Lean

1. Uninstall packages you don't use
2. Run `brew autoremove` regularly
3. Review `brew leaves` periodically
4. Don't install packages "just in case"

## Common Issues

### "Permission denied" Errors

```bash
# Fix permissions
sudo chown -R $(whoami) $(brew --prefix)/*
```

### Disk Space Issues

```bash
# Aggressive cleanup
brew cleanup -s
rm -rf $(brew --cache)
brew autoremove
```

### Conflicting Packages

```bash
# Unlink conflicting
brew unlink package_name

# Then link the one you want
brew link package_name
```

## Quick Reference

```bash
# Full maintenance routine
brew update && \
brew upgrade && \
brew cleanup && \
brew autoremove && \
brew doctor
```

## Reference Files

- **`references/homebrew-commands.md`** - Complete Homebrew command reference
