---
name: Disk Cleanup Patterns
description: Use when user asks about "cleaning Mac", "disk cleanup", "free disk space", "what to delete", "safe to delete", "cache cleanup", "clear storage", or needs guidance on which files are safe to remove on macOS.
version: 0.1.0
allowed-tools: Read, Grep, Glob, Bash
---

# macOS Disk Cleanup Patterns

Comprehensive guide to identifying and safely removing files to reclaim disk space on macOS.

## Safety Classification

### Safe to Delete (Green Zone)

These can be cleaned without risk of data loss or system issues:

| Location | Description | Typical Size |
|----------|-------------|--------------|
| `~/Library/Caches/*` | User application caches | 1-20 GB |
| `/Library/Caches/*` | System-wide caches | 1-5 GB |
| `~/.Trash/*` | User's Trash | Varies |
| `~/Library/Logs/*` | Application logs | 100 MB - 2 GB |
| `/private/var/log/*` | System logs | 100 MB - 1 GB |
| `~/Downloads/*.dmg` | Installer disk images | Varies |
| Browser caches | Chrome, Safari, Firefox | 500 MB - 5 GB |

### Requires Confirmation (Yellow Zone)

Safe to clean but user should understand implications:

| Location | Description | Consideration |
|----------|-------------|---------------|
| Xcode DerivedData | Build cache | Rebuilds take longer |
| node_modules | NPM dependencies | Can reinstall with `npm install` |
| Docker images/volumes | Container data | Check if data is needed |
| Old Downloads | Downloaded files | May want to keep some |
| iOS Simulators | Development simulators | Reinstalls if needed |

### Never Delete (Red Zone)

Do not suggest deleting these without explicit user request:

| Location | Reason |
|----------|--------|
| `~/Documents` | User documents |
| `~/Desktop` | User files |
| `~/Library/Application Support` | App data and settings |
| `~/Library/Preferences` | User preferences |
| `~/Pictures/Photos Library.photoslibrary` | Photos library |
| `~/Music/Music` | Music library |
| Any `.sqlite`, `.db` files | Databases with user data |
| Keychain files | Password storage |

## Cache Locations

### User Caches (`~/Library/Caches/`)

The primary cleanup target. Contains regeneratable cached data.

**Safe deletion process:**
```bash
# Delete contents, keep the folder structure
rm -rf ~/Library/Caches/*
```

**Common large entries:**
- `com.apple.Safari` - Safari cache
- `com.spotify.client` - Spotify cache
- `Google/Chrome` - Chrome cache
- `com.microsoft.VSCode` - VS Code cache

### System Caches (`/Library/Caches/`)

Requires admin privileges. Generally safe but be cautious.

```bash
# Requires sudo
sudo rm -rf /Library/Caches/*
```

### DNS Cache

Separate from file caches but worth flushing:

```bash
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

## Size Estimation Commands

### Quick Overview

```bash
# Home directory breakdown
du -sh ~/Desktop ~/Documents ~/Downloads ~/Movies ~/Music ~/Pictures ~/Library 2>/dev/null | sort -hr

# Top-level sizes in Library
du -sh ~/Library/*/ 2>/dev/null | sort -hr | head -20
```

### Find Large Files

```bash
# Files over 100MB in home
find ~ -type f -size +100M -exec ls -lh {} \; 2>/dev/null | sort -k5 -hr

# Files over 1GB
find ~ -type f -size +1G -exec ls -lh {} \; 2>/dev/null
```

### Find Old Files

```bash
# Files not accessed in 180 days
find ~/Downloads -type f -atime +180 2>/dev/null

# Files not modified in 365 days
find ~ -type f -mtime +365 -size +10M 2>/dev/null
```

## Cleanup Order

For maximum efficiency, clean in this order:

1. **Trash** - Immediate space recovery
2. **User Caches** - Safe, often large
3. **Browser Caches** - Can be significant
4. **Downloads** - Review and clean old files
5. **Developer Caches** - DerivedData, node_modules
6. **System Caches** - Smaller but helps
7. **Logs** - Usually small
8. **Large Files** - Manual review required

## Hidden Space Consumers

### Time Machine Local Snapshots

macOS keeps local Time Machine backups that can consume significant space.

```bash
# List snapshots
tmutil listlocalsnapshots /

# Delete old snapshots (careful!)
tmutil deletelocalsnapshots 2024-01-01-000000
```

### APFS Snapshots

```bash
# View snapshots
diskutil apfs listSnapshots /
```

### Mail Attachments

```bash
du -sh ~/Library/Mail/V*/*/Data/Library/Attachments/ 2>/dev/null
```

### iOS Backups

```bash
du -sh ~/Library/Application\ Support/MobileSync/Backup/ 2>/dev/null
```

## Common Mistakes to Avoid

1. **Deleting `~/Library`** - Contains critical app data
2. **Deleting `.app` bundles in `~/Library`** - These are applications
3. **Cleaning `~/Library/Application Support`** - Contains app settings
4. **Removing preference files** - Breaks app configurations
5. **Deleting Keychain** - Loses all saved passwords
6. **Using aggressive "cleaner" apps** - Often delete too much

## Post-Cleanup Verification

After cleaning:

```bash
# Verify disk space recovered
df -h /

# Check system still works
# Restart may be needed for system cache changes
```

## Automated Cleanup Recommendations

For regular maintenance:

1. **Weekly**: Empty Trash, clear browser cache
2. **Monthly**: Clean user caches, review Downloads
3. **Quarterly**: Developer cache cleanup, large file review
4. **As needed**: Docker cleanup, iOS simulator cleanup

## Reference Files

For detailed location lists and cleanup scripts:

- **`references/cache-locations.md`** - Complete cache location reference
- **`references/cleanup-commands.md`** - Ready-to-use cleanup commands
