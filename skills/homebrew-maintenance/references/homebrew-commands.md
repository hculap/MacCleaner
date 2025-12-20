# Homebrew Commands Reference

## Installation Info

```bash
# Homebrew prefix
brew --prefix

# Cache location
brew --cache

# Cellar location
brew --cellar

# Repository location
brew --repo
```

## Update & Upgrade

```bash
# Update Homebrew
brew update

# Upgrade all
brew upgrade

# Upgrade specific package
brew upgrade package_name

# Upgrade casks
brew upgrade --cask

# List outdated
brew outdated
brew outdated --cask
```

## Cleanup

```bash
# Basic cleanup
brew cleanup

# Dry run
brew cleanup -n

# Scrub cache
brew cleanup -s

# Remove downloads older than X days
brew cleanup --prune=30

# Remove specific package old versions
brew cleanup package_name
```

## Autoremove

```bash
# Dry run
brew autoremove --dry-run

# Remove orphaned dependencies
brew autoremove
```

## Package Management

```bash
# Search
brew search keyword

# Install
brew install package_name
brew install --cask app_name

# Uninstall
brew uninstall package_name
brew uninstall --cask app_name

# Reinstall
brew reinstall package_name

# Info
brew info package_name
```

## Listing

```bash
# All installed
brew list

# Only formulae
brew list --formula

# Only casks
brew list --cask

# With versions
brew list --versions

# Top-level only (not dependencies)
brew leaves
```

## Dependencies

```bash
# Show deps of package
brew deps package_name

# Show all deps (recursive)
brew deps --tree package_name

# Show what uses this package
brew uses package_name

# Show installed packages that use this
brew uses --installed package_name

# Show missing deps
brew missing
```

## Health & Diagnostics

```bash
# Full diagnostic
brew doctor

# Config info
brew config

# Check for issues
brew missing
```

## Services

```bash
# List all services
brew services list

# Start service
brew services start service_name

# Stop service
brew services stop service_name

# Restart service
brew services restart service_name

# Run once (don't auto-start)
brew services run service_name

# Cleanup old service files
brew services cleanup
```

## Taps

```bash
# List taps
brew tap

# Add tap
brew tap user/repo

# Remove tap
brew untap user/repo

# Tap info
brew tap-info tap_name
```

## Linking

```bash
# Link package
brew link package_name

# Force link
brew link --overwrite package_name

# Unlink package
brew unlink package_name
```

## Pinning

```bash
# Pin (prevent upgrades)
brew pin package_name

# Unpin
brew unpin package_name

# List pinned
brew list --pinned
```

## Cask-Specific

```bash
# Cask info
brew info --cask app_name

# Open app homepage
brew home --cask app_name

# Reinstall cask
brew reinstall --cask app_name

# Zap (remove preferences too)
brew uninstall --cask --zap app_name
```

## Bundle (Backup/Restore)

```bash
# Create Brewfile
brew bundle dump

# Create at specific location
brew bundle dump --file=~/Brewfile

# Install from Brewfile
brew bundle install

# Check what's missing
brew bundle check

# Clean up unlisted packages
brew bundle cleanup
```

## Cache Management

```bash
# Show cache
ls $(brew --cache)

# Cache size
du -sh $(brew --cache)

# Clear cache
rm -rf $(brew --cache)
```

## Size Analysis

```bash
# Total Homebrew size
du -sh $(brew --prefix)

# Individual package sizes
brew list --formula | while read pkg; do
  echo -n "$pkg: "
  du -sh $(brew --cellar)/$pkg 2>/dev/null | cut -f1
done | sort -hr -k2

# Cache size
du -sh $(brew --cache)

# Caskroom size
du -sh $(brew --prefix)/Caskroom
```

## Maintenance Routine

```bash
#!/bin/bash
echo "=== Homebrew Maintenance ==="

echo "Updating..."
brew update

echo "Upgrading..."
brew upgrade

echo "Cleaning up..."
brew cleanup -s

echo "Removing orphans..."
brew autoremove

echo "Checking health..."
brew doctor

echo "Done!"
```
