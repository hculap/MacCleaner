---
name: Developer Cleanup
description: Use when user asks about "node_modules cleanup", "DerivedData", "Xcode cleanup", "build artifacts", "target folder", "development cache", "clean build", "developer disk space", or needs guidance on cleaning up development environment files on macOS.
version: 0.1.0
allowed-tools: Bash, Glob, Grep
---

# Developer Environment Cleanup

Cleaning up development build artifacts, dependencies, and caches on macOS.

## Node.js / JavaScript

### node_modules Directories

The `node_modules` folder can be massive (100MB - 1GB per project).

#### Find All node_modules

```bash
# List with sizes
find ~ -name "node_modules" -type d -prune 2>/dev/null | while read d; do
  du -sh "$d" 2>/dev/null
done | sort -hr

# Count total
find ~ -name "node_modules" -type d -prune 2>/dev/null | wc -l
```

#### Safe Cleanup Strategy

1. **Keep active projects** - Projects you're working on
2. **Remove old projects** - Haven't touched in months
3. **Re-install is easy** - Just run `npm install`

#### Remove node_modules from Specific Project

```bash
rm -rf /path/to/project/node_modules
```

#### Bulk Cleanup (Careful!)

```bash
# Remove all node_modules older than 30 days
find ~ -name "node_modules" -type d -mtime +30 -prune -exec rm -rf {} \;
```

### Package Manager Caches

| Manager | Cache Location | Clean Command |
|---------|----------------|---------------|
| npm | `~/.npm/` | `npm cache clean --force` |
| yarn | `~/.yarn/cache/` | `yarn cache clean` |
| pnpm | `~/.pnpm-store/` | `pnpm store prune` |

### Build Output Directories

Clean these from each project:

- `dist/`
- `build/`
- `.next/`
- `.nuxt/`
- `.output/`

## Xcode / iOS Development

### DerivedData

Build caches for Xcode projects. Safe to delete.

```bash
# Size check
du -sh ~/Library/Developer/Xcode/DerivedData

# Clean all
rm -rf ~/Library/Developer/Xcode/DerivedData/*

# Clean in Xcode: Product → Clean Build Folder (Shift+Cmd+K)
```

**Typical size**: 5-100 GB

### Archives

Old app builds for App Store submissions.

```bash
# Size check
du -sh ~/Library/Developer/Xcode/Archives

# Location in Finder
open ~/Library/Developer/Xcode/Archives
```

**Keep**: Recent releases, builds you might need
**Delete**: Old test builds, superseded versions

### iOS Simulator Data

```bash
# Size check
du -sh ~/Library/Developer/CoreSimulator

# Remove unavailable simulators
xcrun simctl delete unavailable

# List all simulators
xcrun simctl list devices

# Delete specific simulator
xcrun simctl delete <UUID>

# Reset all simulator content
xcrun simctl erase all
```

### iOS Device Support

Debug symbols for physical devices.

```bash
# Size check
du -sh ~/Library/Developer/Xcode/iOS\ DeviceSupport

# Safe to delete old iOS versions you no longer test on
```

### watchOS/tvOS Device Support

```bash
du -sh ~/Library/Developer/Xcode/watchOS\ DeviceSupport
du -sh ~/Library/Developer/Xcode/tvOS\ DeviceSupport
```

### Module Cache

```bash
# Size check
du -sh ~/Library/Developer/Xcode/DerivedData/ModuleCache*

# Clean (within DerivedData, cleaned together)
```

### CocoaPods Cache

```bash
du -sh ~/Library/Caches/CocoaPods
pod cache clean --all
```

## Rust

### Target Directories

```bash
# Find all target directories
find ~ -name "target" -type d -path "*/.cargo/*" -prune -o -name "target" -type d -print 2>/dev/null | while read d; do
  du -sh "$d" 2>/dev/null
done | sort -hr
```

#### Clean Individual Project

```bash
cargo clean
# Run in project directory
```

#### Registry Cache

```bash
du -sh ~/.cargo/registry/cache
# Safe to delete, will re-download
```

## Python

### Virtual Environments

```bash
# Find venvs
find ~ -type d \( -name "venv" -o -name ".venv" -o -name "env" \) 2>/dev/null | while read d; do
  du -sh "$d" 2>/dev/null
done | sort -hr
```

Remove and recreate with `python -m venv venv`.

### Pip Cache

```bash
du -sh ~/Library/Caches/pip
pip cache purge
```

### __pycache__ Directories

```bash
# Find all
find ~ -name "__pycache__" -type d 2>/dev/null

# Remove all (safe)
find ~ -name "__pycache__" -type d -exec rm -rf {} \; 2>/dev/null
```

### .pyc Files

```bash
find ~ -name "*.pyc" -delete 2>/dev/null
```

## Java / Kotlin

### Gradle Cache

```bash
du -sh ~/.gradle/caches

# Clean (keeps current versions)
./gradlew clean

# Full cache clean
rm -rf ~/.gradle/caches
```

### Maven Cache

```bash
du -sh ~/.m2/repository

# Clean project
mvn clean

# Remove specific artifacts
rm -rf ~/.m2/repository/com/your/package
```

### Build Directories

- `build/` - Gradle output
- `target/` - Maven output

## Go

### Module Cache

```bash
du -sh ~/go/pkg/mod

# Clean unused modules
go clean -modcache
```

### Build Cache

```bash
# Clean build cache
go clean -cache
```

## VS Code

### Extension Cache

```bash
du -sh ~/.vscode/extensions
```

### Workspace Storage

```bash
du -sh ~/Library/Application\ Support/Code/User/workspaceStorage
```

## JetBrains IDEs

### Caches and Logs

```bash
# IntelliJ IDEA
du -sh ~/Library/Caches/JetBrains/IntelliJIdea*

# All JetBrains
du -sh ~/Library/Caches/JetBrains
```

### Invalidate Caches

Within IDE: File → Invalidate Caches / Restart

## Quick Cleanup Script

```bash
#!/bin/bash
echo "=== Developer Cleanup ==="

echo "Xcode DerivedData:"
du -sh ~/Library/Developer/Xcode/DerivedData 2>/dev/null

echo "iOS Simulators:"
xcrun simctl delete unavailable 2>/dev/null
du -sh ~/Library/Developer/CoreSimulator 2>/dev/null

echo "npm cache:"
npm cache clean --force 2>/dev/null

echo "pip cache:"
pip cache purge 2>/dev/null

echo "Done!"
```

## Best Practices

1. **Before major project work**: Clean old project artifacts
2. **After switching branches**: Clean build caches if issues
3. **Monthly**: Review and clean old project dependencies
4. **Keep .gitignore updated**: Ensure build artifacts aren't committed

## Reference Files

- **`references/dev-cleanup-commands.md`** - All cleanup commands by platform
