---
name: Security Scanning
description: Use when user asks about "Mac security", "malware", "virus", "LaunchAgent", "suspicious activity", "is my Mac safe", "security scan", "infected Mac", or needs guidance on macOS security threats and detection.
version: 0.1.0
allowed-tools: Bash, Read, Glob, Grep
---

# macOS Security Scanning

Identifying malware, suspicious persistence mechanisms, and security threats on macOS.

## macOS Security Architecture

### System Integrity Protection (SIP)

SIP protects core system files from modification.

```bash
csrutil status
```

**SIP should be enabled.** Disabled SIP is a major security risk.

### Gatekeeper

Controls which apps can run based on signing.

```bash
spctl --status
```

**Should show "assessments enabled".**

### XProtect

Apple's built-in malware detection. Updates automatically.

## Primary Threat Vector: LaunchAgents/LaunchDaemons

Most macOS malware persists through LaunchAgents or LaunchDaemons.

### What Are They?

- **LaunchAgents**: Run when user logs in
- **LaunchDaemons**: Run at system boot (before login)

### Locations to Check

| Location | Runs As | Risk Level |
|----------|---------|------------|
| `~/Library/LaunchAgents/` | User | HIGH - Most common malware location |
| `/Library/LaunchAgents/` | User | MEDIUM |
| `/Library/LaunchDaemons/` | Root | MEDIUM |
| `/System/Library/LaunchAgents/` | User | LOW - Apple controlled |
| `/System/Library/LaunchDaemons/` | Root | LOW - Apple controlled |
| `/var/root/Library/LaunchAgents/` | Root | HIGH - Often overlooked |

### Scanning Commands

```bash
# User LaunchAgents (check this first!)
ls -la ~/Library/LaunchAgents/

# System LaunchAgents
ls -la /Library/LaunchAgents/

# System LaunchDaemons
ls -la /Library/LaunchDaemons/

# Root user agents
sudo ls -la /var/root/Library/LaunchAgents/
```

### Reading Plist Files

```bash
plutil -p ~/Library/LaunchAgents/suspicious.plist
```

### Red Flags in Plist Files

- `ProgramArguments` pointing to:
  - `/tmp/`, `/private/tmp/`
  - `/var/tmp/`
  - `/Users/Shared/`
  - Hidden folders (`/.hidden/`)
- Encoded/obfuscated commands (base64, hex)
- Random-looking names
- `RunAtLoad: true` with suspicious paths

## Known Malware Indicators

### 2024 Malware Patterns

| Malware Family | Indicator Files |
|----------------|-----------------|
| BeaverTail | `com.avatar.update.wake.plist` |
| Atomic Stealer | Fake app bundles, trojanized apps |
| XCSSET | Modified Xcode projects |
| Shlayer | Fake Flash Player installers |
| Generic Adware | `com.startup.plist`, `com.pplauncher.plist` |

### Suspicious File Patterns

```bash
# Random-looking names in LaunchAgents
ls ~/Library/LaunchAgents/ | grep -E "^[a-z]{8,}\.plist$"

# Known bad patterns
ls ~/Library/LaunchAgents/ | grep -iE "startup|update|helper|agent" | grep -v com.apple
```

## Malware Staging Locations

Malware often stages in writable locations:

| Location | Why It's Used |
|----------|---------------|
| `/Users/Shared/` | Writable by all users |
| `/private/tmp/` | Temp, often overlooked |
| `/private/var/tmp/` | Persists across reboots |
| `~/.local/` | Hidden, user-writable |
| Hidden folders in `~` | Not visible in Finder |

### Scanning Commands

```bash
# Check /Users/Shared
ls -la /Users/Shared/

# Check temp locations
ls -la /private/tmp/
ls -la /private/var/tmp/

# Find hidden folders in home
ls -la ~/.[a-zA-Z]* | grep -v ".CFUser\|.Trash\|.bash\|.zsh\|.ssh\|.npm\|.config"
```

## Process Analysis

### Suspicious Process Locations

```bash
# Processes from temp locations
ps aux | grep -E "(/tmp|/var/tmp|/Users/Shared)" | grep -v grep

# Unusual root processes
ps aux | awk '$1=="root" && $11 !~ /^\/System|^\/usr/ {print}'
```

### Network Connections

```bash
# Active connections
lsof -i -P | grep ESTABLISHED

# Listening ports
lsof -i -P | grep LISTEN
```

## Browser Security

### Extension Check

```bash
# Chrome extensions
ls ~/Library/Application\ Support/Google/Chrome/Default/Extensions/

# Safari extensions
ls ~/Library/Safari/Extensions/
```

### Browser Hijacking Signs

- Changed default search engine
- Unexpected redirects
- Pop-up ads
- Slow browser performance
- Unknown toolbars

## Login Items

```bash
osascript -e 'tell application "System Events" to get the name of every login item'
```

## Security Tools Recommendations

### Free Tools

| Tool | Purpose |
|------|---------|
| **KnockKnock** | Lists all persistent items |
| **BlockBlock** | Alerts on new persistence |
| **ReiKey** | Detects keyboard event taps |
| **TaskExplorer** | Shows all running tasks |
| **Netiquette** | Network monitor |

### Commercial Options

- Malwarebytes for Mac
- ClamXAV
- Sophos Home

## VirusTotal Integration

For hash-based file checking:

```bash
# Get file hash
shasum -a 256 /path/to/suspicious/file
```

Then check the hash on VirusTotal or via MCP integration.

## Incident Response

If malware is found:

1. **Document everything** - Screenshot or note findings
2. **Don't panic** - Most macOS malware is adware
3. **Quarantine** - Move suspicious files, don't delete yet
4. **Research** - Google the exact plist name
5. **Clean** - Remove after confirming malware
6. **Monitor** - Watch for reappearance

## Safe LaunchAgents (Don't Flag)

These are legitimate:

- `com.apple.*` - Apple services
- `com.google.*` - Google apps
- `com.microsoft.*` - Microsoft apps
- `com.docker.*` - Docker
- `homebrew.*` - Homebrew services
- `com.adobe.*` - Adobe apps
- `com.spotify.*` - Spotify
- `com.dropbox.*` - Dropbox

## Reference Files

- **`references/malware-indicators.md`** - Detailed IOC list
- **`references/security-commands.md`** - All security scanning commands
