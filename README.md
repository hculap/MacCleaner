# MacCleaner

Comprehensive Mac maintenance toolkit for Claude Code. Analyze and clean disk space, optimize memory, and scan for security threats.

## Features

- **Disk Analysis & Cleanup**: Audit disk usage, clean caches, Docker artifacts, build files, and more
- **Memory Management**: Analyze RAM usage, free inactive memory, identify heavy processes
- **Security Scanning**: Detect suspicious LaunchAgents, malware patterns, and security threats
- **Developer Tools Cleanup**: Clean node_modules, Xcode DerivedData, build caches
- **Docker Maintenance**: Prune images, containers, volumes, and build cache
- **Homebrew Cleanup**: Remove outdated packages and clean cache

## Installation

### From Marketplace
```bash
claude plugins install mac-cleaner
```

### Local Development
```bash
claude --plugin-dir /path/to/MacCleaner
```

## Prerequisites

- macOS (tested on macOS 12+)
- For enhanced malware detection: VirusTotal API key (optional)

## Commands

| Command | Description |
|---------|-------------|
| `/mac-cleaner:disk-audit` | Full disk usage audit with detailed report |
| `/mac-cleaner:disk-clean` | Interactive cleanup (caches, docker, builds, etc.) |
| `/mac-cleaner:memory-audit` | RAM usage analysis and memory pressure report |
| `/mac-cleaner:memory-clean` | Free up memory and optimize RAM usage |
| `/mac-cleaner:security-scan` | Scan for malware and security threats |

## Agents

The plugin includes specialized agents that activate automatically:

- **disk-analyzer**: Triggers when you mention disk space, storage, or cleanup
- **ram-analyzer**: Triggers when you mention slow Mac, memory, or RAM issues
- **security-analyzer**: Triggers when you mention security, malware, or suspicious activity

## Configuration

Create `.claude/mac-cleaner.local.md` in your project root:

```yaml
---
large_file_threshold_mb: 100
old_file_days: 180
auto_confirm_cache_cleanup: false
excluded_paths:
  - ~/Documents/Important
  - ~/Projects/active-project
virustotal_enabled: false
---

# MacCleaner Settings

Custom configuration for this project.
```

### VirusTotal Integration (Optional)

For enhanced malware detection with hash-based file scanning:

1. Get a free API key from [VirusTotal](https://www.virustotal.com/gui/join-us)
2. Set the environment variable:
   ```bash
   export VIRUSTOTAL_API_KEY="your-api-key"
   ```
3. Enable in settings: `virustotal_enabled: true`

## What Gets Cleaned

### Safe to Clean (auto-confirmed)
- System caches (`~/Library/Caches/*`)
- Browser caches
- npm/yarn/pnpm cache
- Homebrew cache

### Requires Confirmation
- Large files (>100MB by default)
- Old files (>6 months by default)
- Docker images and volumes
- Xcode DerivedData and Archives
- node_modules directories
- Build artifacts

### Never Touched
- User documents
- Application data
- System files
- Excluded paths from settings

## Documentation

Full documentation is available in the `docs/` directory:

### Getting Started
- **[Getting Started Guide](docs/getting-started.md)** - First steps with MacCleaner

### Tutorials
- **[Your First Disk Cleanup](docs/tutorials/disk-cleanup.md)** - Walk through cleaning your Mac
- **[Memory Optimization](docs/tutorials/memory-optimization.md)** - Understanding and managing RAM
- **[Security Scanning](docs/tutorials/security-scanning.md)** - Scanning for threats

### How-To Guides
- **[Free Up Space Quickly](docs/how-to/free-space-quickly.md)** - When you need space NOW
- **[Clean Docker](docs/how-to/clean-docker.md)** - Docker-specific cleanup
- **[Clean Xcode](docs/how-to/clean-xcode.md)** - Xcode build artifacts
- **[Clean node_modules](docs/how-to/clean-node-modules.md)** - JavaScript projects
- **[Set Up VirusTotal](docs/how-to/setup-virustotal.md)** - Deep malware scanning
- **[Troubleshooting](docs/how-to/troubleshooting.md)** - Common issues

### Reference
- **[Command Reference](docs/reference/commands.md)** - All commands and options
- **[Settings Reference](docs/reference/settings.md)** - Configuration options

### Understanding
- **[What's Safe to Delete](docs/explanations/safe-to-delete.md)** - Cleanup safety zones
- **[How macOS Manages Memory](docs/explanations/macos-memory.md)** - RAM explained
- **[Security Scanning Explained](docs/explanations/security-scanning.md)** - How threats work

## License

MIT
