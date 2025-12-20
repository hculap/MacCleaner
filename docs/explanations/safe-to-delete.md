# What's Safe to Delete on Mac

Understanding which files you can safely remove—and which you should never touch—is essential for effective Mac maintenance. This guide explains the three safety zones and why each category falls where it does.

## The Three Safety Zones

### Green Zone: Always Safe

These files can be deleted without any risk of data loss or system problems. macOS and applications recreate them automatically when needed.

| Location | What It Contains | Why It's Safe |
|----------|------------------|---------------|
| `~/Library/Caches/*` | App temporary data | Apps rebuild caches on demand |
| `/Library/Caches/*` | System-wide caches | System recreates as needed |
| `~/.Trash/*` | Deleted files | You already deleted these |
| `~/Library/Logs/*` | Application logs | Only used for debugging |
| `~/Downloads/*.dmg` | Disk images | Installers after app is installed |
| Browser caches | Cached web pages | Sites reload from internet |
| `~/Library/Developer/Xcode/DerivedData/*` | Xcode build files | Rebuilds when you compile |
| `~/.npm/_cacache/*` | npm package cache | Downloads again when needed |
| `$(brew --cache)/*` | Homebrew downloads | Re-downloads if needed |

**After cleaning Green Zone items:**
- Apps may take slightly longer to open the first time
- Websites may load slightly slower initially
- Xcode projects need to rebuild

No actual data is lost.

### Yellow Zone: Confirm First

These files are generally safe to remove, but you should understand what you're deleting. Some may contain work-in-progress or intentionally saved data.

| Location | What It Contains | Caution |
|----------|------------------|---------|
| `~/Downloads/*` (old files) | Downloaded files | May include files you want to keep |
| `~/Library/Developer/Xcode/Archives/*` | App archives | Needed if you submit to App Store |
| `~/Library/Developer/CoreSimulator/Devices/*` | iOS Simulators | Delete only unused simulators |
| `node_modules/` directories | JS dependencies | Can regenerate with `npm install` |
| `target/` directories | Rust build output | Regenerates with `cargo build` |
| `.venv/` directories | Python environments | Regenerates with `pip install` |
| Docker images/containers | Container data | May include custom configurations |
| Homebrew packages | Installed software | Only remove if truly unused |

**Before cleaning Yellow Zone:**
- Check if you have active projects using these files
- Consider if you can easily regenerate them
- Review Docker volumes for persistent data

### Red Zone: Never Delete

These files should never be removed. Deleting them causes data loss or system instability.

| Location | What It Contains | Why Never Delete |
|----------|------------------|------------------|
| `~/Documents/*` | Your documents | Personal data |
| `~/Desktop/*` | Desktop files | Personal data |
| `~/Pictures/*` | Photos | Personal media |
| `~/Movies/*` | Videos | Personal media |
| `~/Music/*` | Music library | Personal media |
| `/System/*` | macOS system files | Mac won't boot |
| `/usr/*` | Unix utilities | System functionality |
| `/Applications/*` | Installed apps | Your software |
| `~/Library/Application Support/*` | App data | Settings, saves, licenses |
| `~/Library/Preferences/*` | App preferences | Your customizations |
| `~/.ssh/*` | SSH keys | Server access |
| `~/.gnupg/*` | GPG keys | Encryption keys |

MacCleaner never touches Red Zone files automatically.

## Understanding Caches

Many users worry about deleting caches. Here's why they're safe:

### What Are Caches?

Caches store copies of data for faster access. Instead of:
- Re-downloading a webpage → load from cache
- Re-rendering app graphics → load from cache
- Re-compiling code → load from cache

### Why Deleting Caches Is Safe

1. **Caches are copies** - Original data exists elsewhere
2. **Apps expect cache loss** - They rebuild caches automatically
3. **macOS manages caches** - System prunes old caches anyway
4. **No user data in caches** - Only derived/temporary data

### What Happens After Cache Deletion

| Action | Effect | Duration |
|--------|--------|----------|
| Open browser | Pages load from internet, not cache | Few seconds slower |
| Open app | App rebuilds internal cache | Few seconds slower |
| Build Xcode project | Full rebuild instead of incremental | Minutes for large projects |
| Run npm install | Downloads all packages fresh | Depends on project size |

After the first use, everything returns to normal speed.

## Special Cases

### Docker

Docker deserves special attention because it can consume enormous space:

- **Images**: Safe to prune unused images
- **Containers**: Safe to remove stopped containers (unless you need their logs)
- **Volumes**: CAUTION - May contain persistent data like databases
- **Build cache**: Safe to prune

Before pruning Docker volumes, check what data they contain:
```bash
docker volume ls
docker volume inspect <volume_name>
```

### Xcode Simulators

iOS/tvOS/watchOS simulators can use 1-2 GB each. Safe to delete:
- Simulators for OS versions you don't target
- Duplicate simulators
- Simulators you haven't used in months

Keep simulators for:
- OS versions your app supports
- Devices you actively test on

### node_modules

Every JavaScript project has a `node_modules` folder that can be 100MB to 1GB+.

**Safe to delete if:**
- Project is inactive/archived
- You have `package.json` and `package-lock.json`
- You can run `npm install` to restore

**Don't delete if:**
- Project has local modifications to dependencies
- You're offline and can't re-download
- Build process is complex and undocumented

## How MacCleaner Protects You

MacCleaner implements multiple safety layers:

1. **Zone classification**: Files are categorized by safety level
2. **Confirmation prompts**: Yellow Zone items require explicit approval
3. **Never-touch list**: Red Zone files are excluded from all operations
4. **Excluded paths**: You can add custom exclusions in settings
5. **Trash preference**: User files go to Trash, not permanent deletion

## Configuring Exclusions

Add paths you want to protect in `.claude/mac-cleaner.local.md`:

```yaml
---
excluded_paths:
  - ~/Documents/Critical-Project
  - ~/Downloads/Keep-These
  - ~/Library/Application Support/ImportantApp
---
```

These paths are skipped during all cleanup operations.

## Recovery Options

If you accidentally delete something:

1. **Check Trash first** - MacCleaner moves user files to Trash
2. **Time Machine** - Restore from backup if available
3. **Regenerate** - Most cleaned files can be regenerated:
   - Caches: Just use the app
   - node_modules: Run `npm install`
   - DerivedData: Build your project
   - Docker images: Pull again

## Summary

| Zone | Action | Examples |
|------|--------|----------|
| Green | Delete freely | Caches, logs, build artifacts |
| Yellow | Confirm first | Downloads, Docker, dependencies |
| Red | Never delete | Documents, Photos, System files |

When in doubt, run an audit first and review what MacCleaner suggests. The tool always shows you what it plans to clean before taking action.
