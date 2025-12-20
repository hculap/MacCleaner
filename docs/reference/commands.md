# Command Reference

Complete reference for all MacCleaner commands, including arguments, options, and examples.

---

## /mac-cleaner:disk-audit

Analyze disk usage and identify cleanup opportunities.

### Usage

```
/mac-cleaner:disk-audit [--output <path>]
```

### Arguments

| Argument | Required | Description |
|----------|----------|-------------|
| `--output <path>` | No | Save report to specified file path |

### What It Scans

- Overall disk usage (total, used, available, percentage)
- Home directory breakdown (Documents, Downloads, Desktop, etc.)
- Developer directories (Xcode, package managers, Docker)
- System caches and logs
- Trash contents
- Homebrew cache
- Large files (>100MB)
- Old downloads (>30 days)
- node_modules directories

### Output

Produces a markdown report with:
- Summary statistics
- Top space consumers table
- Cleanup opportunities with safety ratings
- Large files list
- Recommendations

### Examples

**Basic audit:**
```
/mac-cleaner:disk-audit
```

**Save report to file:**
```
/mac-cleaner:disk-audit --output ~/Desktop/disk-report.md
```

---

## /mac-cleaner:disk-clean

Interactive disk cleanup with user confirmation.

### Usage

```
/mac-cleaner:disk-clean [--type <category>]
```

### Arguments

| Argument | Required | Description |
|----------|----------|-------------|
| `--type <category>` | No | Clean specific category only |

### Categories

| Category | Description |
|----------|-------------|
| `cache` | System and application caches |
| `docker` | Docker images, containers, volumes, build cache |
| `builds` | Build artifacts (DerivedData, node_modules, target/) |
| `large` | Large files above threshold |
| `old` | Old files above age threshold |
| `homebrew` | Homebrew cache and orphaned packages |
| `all` | Run all categories interactively |

### Interactive Mode

Without `--type`, presents a multi-select menu of categories.

### Confirmation Behavior

- **Caches**: Single confirmation for all cache cleanup
- **Docker**: Choice of cleanup aggressiveness level
- **Builds**: Checklist of specific directories to clean
- **Large/Old files**: Individual file review
- **Homebrew**: Shows what will be removed first

### Examples

**Interactive cleanup:**
```
/mac-cleaner:disk-clean
```

**Clean only caches:**
```
/mac-cleaner:disk-clean --type cache
```

**Clean Docker:**
```
/mac-cleaner:disk-clean --type docker
```

**Clean all build artifacts:**
```
/mac-cleaner:disk-clean --type builds
```

---

## /mac-cleaner:memory-audit

Analyze RAM usage and memory pressure.

### Usage

```
/mac-cleaner:memory-audit
```

### Arguments

This command takes no arguments.

### What It Analyzes

- Memory pressure status (OK, WARN, CRITICAL)
- Memory breakdown by type (wired, active, inactive, compressed, free)
- Total system RAM
- Top 15 memory-consuming processes
- Swap usage
- Memory by application category (browsers, Electron apps, Docker, IDEs)
- System architecture (Intel vs Apple Silicon)

### Output

Produces a report with:
- Memory pressure indicator
- Memory breakdown table
- Top processes by memory usage
- Application category totals
- Swap analysis
- Recommendations based on findings

### Understanding the Output

**Memory Pressure** is the key metric:
- **OK**: System is healthy
- **WARN**: System is under some pressure
- **CRITICAL**: Immediate action recommended

**Memory types:**
- **Wired**: Kernel memory, cannot be freed
- **Active**: Currently used by apps
- **Inactive**: Recently used, can be freed instantly
- **Compressed**: Active memory compressed to save space
- **Free**: Immediately available

### Examples

**Run memory audit:**
```
/mac-cleaner:memory-audit
```

---

## /mac-cleaner:memory-clean

Free up RAM by purging caches and managing processes.

### Usage

```
/mac-cleaner:memory-clean [--purge] [--kill <process>]
```

### Arguments

| Argument | Required | Description |
|----------|----------|-------------|
| `--purge` | No | Run `sudo purge` to free inactive memory |
| `--kill <process>` | No | Terminate specific process by name |

### Modes

**Interactive Mode** (no arguments):
1. Shows current memory status
2. Shows top memory consumers
3. Asks what action to take:
   - Purge inactive memory
   - Show apps to close
   - Kill a specific process
   - Both purge and show suggestions

**Direct Purge Mode** (`--purge`):
- Runs `sudo purge` immediately
- Clears disk cache from RAM
- Requires administrator password

**Kill Mode** (`--kill`):
- Terminates the specified process
- Uses graceful quit first, then force kill

### What Purge Does

The `purge` command:
- Clears the disk cache from RAM
- Does NOT close any applications
- Does NOT delete any data
- May cause temporary slowdown as caches rebuild

### Examples

**Interactive cleanup:**
```
/mac-cleaner:memory-clean
```

**Purge memory directly:**
```
/mac-cleaner:memory-clean --purge
```

**Kill specific app:**
```
/mac-cleaner:memory-clean --kill "Google Chrome"
```

---

## /mac-cleaner:security-scan

Scan for malware, suspicious files, and security threats.

### Usage

```
/mac-cleaner:security-scan [--deep]
```

### Arguments

| Argument | Required | Description |
|----------|----------|-------------|
| `--deep` | No | Include VirusTotal hash checking (requires API key) |

### What It Scans

**System Security:**
- System Integrity Protection (SIP) status
- Gatekeeper status

**Persistence Mechanisms:**
- User LaunchAgents (`~/Library/LaunchAgents/`)
- System LaunchAgents (`/Library/LaunchAgents/`)
- System LaunchDaemons (`/Library/LaunchDaemons/`)
- Root LaunchAgents (`/var/root/Library/LaunchAgents/`)
- Login Items

**Known Malware Patterns:**
- BeaverTail indicators
- Common adware patterns
- Suspicious naming patterns

**Suspicious Locations:**
- `/Users/Shared/`
- `/private/tmp/`
- Hidden directories in home folder

**Running Processes:**
- Processes running from temp directories
- Unusual root processes

**Browser Extensions:**
- Chrome extension count
- Safari extensions

### Risk Levels

| Level | Meaning |
|-------|---------|
| CRITICAL | Known malware found, SIP disabled, active threats |
| HIGH | Suspicious unsigned items, processes from temp locations |
| MEDIUM | Unknown LaunchAgents, many browser extensions |
| LOW | Old unused items, minor configuration issues |
| CLEAN | No suspicious indicators |

### Deep Scan Mode

With `--deep` flag and VirusTotal configured:
1. Hashes suspicious files with SHA-256
2. Queries VirusTotal for malware matches
3. Reports detection results from multiple antivirus engines

Requires `VIRUSTOTAL_API_KEY` environment variable.

### Safe LaunchAgents

These are NOT flagged as suspicious:
- `com.apple.*` - Apple system services
- `com.google.*` - Google applications
- `com.microsoft.*` - Microsoft applications
- `com.docker.*` - Docker Desktop
- `homebrew.*` - Homebrew services
- `com.adobe.*` - Adobe applications

### Examples

**Standard security scan:**
```
/mac-cleaner:security-scan
```

**Deep scan with VirusTotal:**
```
/mac-cleaner:security-scan --deep
```

---

## Command Comparison

| Command | Purpose | Destructive | Requires Confirmation |
|---------|---------|-------------|----------------------|
| disk-audit | Analysis only | No | No |
| disk-clean | Clean disk | Yes | Yes |
| memory-audit | Analysis only | No | No |
| memory-clean | Free memory | Partially | Yes (for kill) |
| security-scan | Analysis only | No | No |

---

## Common Workflows

### Monthly Maintenance

```
/mac-cleaner:disk-audit
/mac-cleaner:disk-clean --type cache
/mac-cleaner:disk-clean --type homebrew
```

### When Disk Is Full

```
/mac-cleaner:disk-audit
/mac-cleaner:disk-clean --type all
```

### When Mac Is Slow

```
/mac-cleaner:memory-audit
/mac-cleaner:memory-clean --purge
```

### After Security Concern

```
/mac-cleaner:security-scan
/mac-cleaner:security-scan --deep
```
