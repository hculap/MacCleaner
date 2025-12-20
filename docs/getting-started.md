# Getting Started with MacCleaner

MacCleaner is a Claude Code plugin that helps you maintain your Mac by analyzing disk usage, optimizing memory, and scanning for security threats.

## Installation

### From Claude Code Marketplace

```bash
claude plugins install mac-cleaner
```

### Local Installation (Development)

```bash
claude --plugin-dir /path/to/MacCleaner
```

## Your First Command

After installation, try running a disk audit to see what's taking up space:

```
/mac-cleaner:disk-audit
```

This scans your Mac and produces a detailed report showing:
- Overall disk usage (total, used, available)
- Largest directories in your home folder
- Developer tool caches (Xcode, npm, etc.)
- Docker usage (if installed)
- Cleanup opportunities with estimated space savings

## Understanding the Report

When you run disk-audit, you'll see output like:

```
## Disk Usage Analysis

**Overall Status**: 180 GB used of 500 GB (36% full)

### Top Space Consumers
| Directory         | Size    |
|-------------------|---------|
| ~/Library         | 45 GB   |
| ~/Documents       | 32 GB   |
| ~/Downloads       | 18 GB   |

### Cleanup Opportunities
| Category          | Size   | Safety  |
|-------------------|--------|---------|
| User Caches       | 8.2 GB | Safe    |
| Xcode DerivedData | 12 GB  | Safe    |
| Docker            | 25 GB  | Confirm |
```

**Safety levels:**
- **Safe**: Can be cleaned without risk
- **Confirm**: Requires your approval before deletion
- **Review**: You should check these files manually

## Available Commands

| Command | What it does |
|---------|--------------|
| `/mac-cleaner:disk-audit` | Analyze disk usage and find cleanup opportunities |
| `/mac-cleaner:disk-clean` | Interactive cleanup with confirmation prompts |
| `/mac-cleaner:memory-audit` | Check RAM usage and memory pressure |
| `/mac-cleaner:memory-clean` | Free up memory by purging caches |
| `/mac-cleaner:security-scan` | Scan for malware and suspicious activity |

## Automatic Agents

MacCleaner includes smart agents that activate when you mention certain topics:

- **"My Mac is running out of space"** → Disk analyzer activates
- **"My Mac feels slow"** → RAM analyzer activates
- **"Is my Mac infected?"** → Security analyzer activates

You don't need to run commands explicitly—just describe your problem naturally.

## Next Steps

1. **[Clean your disk](tutorials/disk-cleanup.md)** - Walk through your first cleanup
2. **[Optimize memory](tutorials/memory-optimization.md)** - Understand and manage RAM
3. **[Run a security scan](tutorials/security-scanning.md)** - Check for threats

## Quick Tips

- **Start with an audit** before cleaning—understand what's using space first
- **Cache cleanup is always safe**—macOS rebuilds caches automatically
- **Docker can be a huge space hog**—check it regularly if you use containers
- **node_modules add up fast**—old projects can waste gigabytes

## Getting Help

If something isn't working:
1. Check the [troubleshooting guide](how-to/troubleshooting.md)
2. Make sure you're on macOS 12 or later
3. Some features require Homebrew or Docker to be installed
