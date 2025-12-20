# Tutorial: Your First Disk Cleanup

This tutorial walks you through analyzing your Mac's disk usage and safely cleaning up unnecessary files to reclaim space.

## What You'll Learn

- How to audit your disk usage
- How to interpret the results
- How to safely clean different categories of files
- What to avoid deleting

## Prerequisites

- MacCleaner plugin installed
- 10-15 minutes

## Step 1: Run a Disk Audit

Start by understanding what's using your disk space:

```
/mac-cleaner:disk-audit
```

Wait for the scan to complete. Depending on your disk size, this takes 30 seconds to a few minutes.

## Step 2: Review the Report

The audit produces a detailed report. Here's what to look for:

### Overall Status

```
**Overall Status**: 385 GB used of 500 GB (77% full)
```

If you're above 80% full, your Mac may slow down. macOS needs free space for virtual memory and temporary files.

### Top Space Consumers

```
### Top Space Consumers
| Directory              | Size    |
|------------------------|---------|
| ~/Library              | 89 GB   |
| ~/Library/Developer    | 45 GB   |
| ~/Documents            | 67 GB   |
| ~/Downloads            | 23 GB   |
```

`~/Library` and `~/Library/Developer` are usually the biggest offenders because they contain:
- Application caches
- Xcode build files
- iOS Simulators
- Package manager caches

### Cleanup Opportunities

```
### Cleanup Opportunities
| Category              | Size    | Safety  |
|-----------------------|---------|---------|
| User Caches           | 12.3 GB | Safe    |
| Xcode DerivedData     | 18.5 GB | Safe    |
| iOS Simulators        | 8.2 GB  | Safe    |
| npm cache             | 3.1 GB  | Safe    |
| Docker                | 34 GB   | Confirm |
| Old Downloads         | 5.6 GB  | Review  |
```

Focus on **Safe** items first—these can be deleted without any risk.

## Step 3: Start Cleaning

Run the interactive cleanup:

```
/mac-cleaner:disk-clean
```

You'll be asked which categories to clean:

```
Which cleanup categories would you like to run?
☑ Caches (system, app, browser)
☑ Build artifacts (Xcode, node_modules, etc.)
☐ Docker (images, containers, volumes)
☐ Large files (>100MB)
☐ Old files (>6 months)
☐ Homebrew (cache, outdated packages)
```

Select the categories you want. For your first cleanup, start with:
- **Caches** - Always safe
- **Build artifacts** - Safe if you can rebuild projects

## Step 4: Confirm Each Action

MacCleaner asks for confirmation before deleting anything significant:

```
Found 12.3 GB in ~/Library/Caches/

This includes:
- Browser caches (Chrome, Safari): 4.2 GB
- Application caches: 8.1 GB

Clean these cache files? [y/n]
```

Type `y` to proceed or `n` to skip.

### What Happens When You Clean Caches

- **Browser caches**: Pages may load slightly slower initially as they re-download
- **App caches**: Apps may take a moment longer to open the first time
- **System caches**: macOS rebuilds them automatically

None of your data is lost—only temporary files that speed up repeated operations.

## Step 5: Review Results

After cleanup, you'll see a summary:

```
## Cleanup Complete

### Actions Performed
| Category        | Space Freed |
|-----------------|-------------|
| User Caches     | 12.3 GB     |
| Xcode Data      | 18.5 GB     |
| npm cache       | 3.1 GB      |

### Disk Status
- **Before**: 115 GB available
- **After**: 149 GB available
- **Recovered**: 33.9 GB
```

## What NOT to Delete

MacCleaner protects these automatically, but you should know:

| Never Delete | Why |
|--------------|-----|
| ~/Documents | Your personal files |
| ~/Desktop | Your desktop files |
| ~/Pictures, ~/Movies | Your media |
| Application files in /Applications | Your installed apps |
| System files | macOS won't work without them |

## Cleaning Specific Categories

### If You Use Docker

Docker can easily consume 50+ GB. Run cleanup with Docker selected:

```
/mac-cleaner:disk-clean --type docker
```

Choose your cleanup level:
- **Remove unused images only** - Safest, removes images not used by containers
- **Remove all unused resources** - More aggressive, includes stopped containers
- **Remove everything including volumes** - Most aggressive, may delete data

### If You're a Developer

Developer tools create massive caches:

```
/mac-cleaner:disk-clean --type builds
```

This cleans:
- Xcode DerivedData (rebuilds automatically)
- iOS Simulators you're not using
- node_modules in old projects
- Rust target/ directories
- Python virtual environments

## Tips for Keeping Your Mac Clean

1. **Run disk-audit monthly** - Catch space issues early
2. **Clean caches after major updates** - System updates leave behind old caches
3. **Review Downloads folder** - Old installers and DMGs pile up
4. **Prune Docker regularly** - If you use Docker, make it a habit

## Next Steps

- **[How to free up space quickly](../how-to/free-space-quickly.md)** - When you need space NOW
- **[What's safe to delete](../explanations/safe-to-delete.md)** - Deeper explanation of safety zones
- **[Clean Docker specifically](../how-to/clean-docker.md)** - Detailed Docker cleanup guide
