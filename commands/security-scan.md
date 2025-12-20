---
description: Scan for malware, suspicious LaunchAgents, and security threats on macOS
allowed-tools: Bash, Read, Glob, Grep, Task
argument-hint: [--deep]
---

# Security Scan Command

Perform comprehensive security scan: $ARGUMENTS

## Step 1: Parse Arguments

Parse `$ARGUMENTS` to extract:
- `--deep`: Include VirusTotal hash checking for suspicious files (requires API key)

## Step 2: System Integrity Check

```bash
echo "=== System Integrity Protection ==="
csrutil status
```

**SIP should be enabled.** Disabled SIP is a significant security risk.

## Step 3: Gatekeeper Status

```bash
echo "=== Gatekeeper Status ==="
spctl --status
```

**Should show "assessments enabled".**

## Step 4: Scan LaunchAgents

#### User LaunchAgents (Primary malware target)
```bash
echo "=== User LaunchAgents ==="
ls -la ~/Library/LaunchAgents/ 2>/dev/null || echo "Directory empty or doesn't exist"
```

#### System LaunchAgents
```bash
echo "=== System LaunchAgents ==="
ls -la /Library/LaunchAgents/ 2>/dev/null
```

#### System LaunchDaemons
```bash
echo "=== System LaunchDaemons ==="
ls -la /Library/LaunchDaemons/ 2>/dev/null
```

#### Root User LaunchAgents (Often overlooked)
```bash
echo "=== Root LaunchAgents ==="
sudo ls -la /var/root/Library/LaunchAgents/ 2>/dev/null || echo "Empty or no access"
```

## Step 5: Analyze Plist Files

For each plist found, read and analyze:

```bash
# List all plist contents
for plist in ~/Library/LaunchAgents/*.plist; do
  echo "--- $plist ---"
  plutil -p "$plist" 2>/dev/null | head -20
done
```

**Red flags to check for:**
- `ProgramArguments` pointing to `/tmp`, `/var/tmp`, `/Users/Shared`
- Encoded or obfuscated commands (base64, hex)
- `RunAtLoad: true` with suspicious program paths
- Random-looking filenames or identifiers
- Programs in hidden directories (`.folder`)

## Step 6: Known Malware Patterns

```bash
echo "=== Checking Known Malware Indicators ==="

# BeaverTail indicator
ls ~/Library/LaunchAgents/com.avatar.update.wake.plist 2>/dev/null && echo "WARNING: Possible BeaverTail indicator found"

# Generic adware patterns
ls ~/Library/LaunchAgents/com.startup.plist 2>/dev/null && echo "WARNING: Possible adware indicator"
ls ~/Library/LaunchAgents/com.pplauncher.plist 2>/dev/null && echo "WARNING: Possible adware indicator"

# Check for suspicious naming patterns
ls ~/Library/LaunchAgents/ 2>/dev/null | grep -E "^[a-z]{8,}\.plist$" && echo "WARNING: Suspicious random-looking plist names found"
```

## Step 7: Scan Staging Locations

```bash
echo "=== Common Malware Locations ==="

# /Users/Shared (writable by all users)
ls -la /Users/Shared/ 2>/dev/null | grep -v "^total\|^\.\|^SC Info"

# Temp directories
ls -la /private/tmp/ 2>/dev/null | head -20
ls -la /private/var/tmp/ 2>/dev/null | head -20

# Hidden folders in home
ls -la ~/.[a-zA-Z]* 2>/dev/null | grep -v ".CFUser\|.Trash\|.bash\|.zsh\|.ssh\|.npm\|.config\|.cache\|.docker\|.kube\|.local\|.gnupg\|.gitconfig"
```

## Step 8: Login Items

```bash
echo "=== Login Items ==="
osascript -e 'tell application "System Events" to get the name of every login item' 2>/dev/null
```

## Step 9: Running Processes Check

```bash
echo "=== Suspicious Process Locations ==="
ps aux | grep -E "(/tmp|/var/tmp|/Users/Shared|/private/tmp)" | grep -v grep

echo "=== Unusual Root Processes ==="
ps aux | awk '$1=="root" && $11 !~ /^\/System|^\/usr\/libexec|^\/usr\/sbin|^\/sbin|^\/kernel|^\/Library\/Apple/ {print $11}' | sort -u | head -20
```

## Step 10: Browser Extension Check

```bash
echo "=== Browser Extensions ==="

# Chrome extensions count
chrome_ext=$(ls ~/Library/Application\ Support/Google/Chrome/Default/Extensions/ 2>/dev/null | wc -l)
echo "Chrome extensions: $chrome_ext"

# Safari extensions
ls ~/Library/Safari/Extensions/ 2>/dev/null
```

## Step 11: Deep Scan (if --deep flag)

If `--deep` is specified and VirusTotal MCP is configured:

1. Hash suspicious files found in previous steps
2. Query VirusTotal for each hash
3. Report any positive detections

```bash
# Hash a suspicious file
shasum -a 256 /path/to/suspicious/file
```

Then use VirusTotal MCP tools to check the hash.

## Step 12: Generate Report

```markdown
# Security Scan Report

**Generated**: [timestamp]
**Scan Type**: [Standard / Deep]

## System Security Status

| Check | Status |
|-------|--------|
| System Integrity Protection | [Enabled / DISABLED (WARNING)] |
| Gatekeeper | [Enabled / Disabled] |

## Overall Risk Assessment

**Risk Level**: [CLEAN / LOW / MEDIUM / HIGH / CRITICAL]

## LaunchAgents & LaunchDaemons

### User LaunchAgents (`~/Library/LaunchAgents/`)
| File | Status | Analysis |
|------|--------|----------|
| name.plist | [OK / SUSPICIOUS] | [notes] |

### System LaunchAgents (`/Library/LaunchAgents/`)
| File | Status | Analysis |
|------|--------|----------|

### System LaunchDaemons (`/Library/LaunchDaemons/`)
| File | Status | Analysis |
|------|--------|----------|

## Findings

### [CRITICAL] Findings
- [List any critical security issues]

### [HIGH] Risk Items
- [List high risk items]

### [MEDIUM] Risk Items
- [List medium risk items]

### [LOW] Risk / Informational
- [List low risk or informational items]

## Suspicious Locations Checked
| Location | Finding |
|----------|---------|
| /Users/Shared/ | [Clean / Items found] |
| /private/tmp/ | [Clean / Items found] |
| Hidden home folders | [Clean / Items found] |

## Process Analysis
- Suspicious processes from temp locations: [count]
- Unusual root processes: [count]

## Recommendations

1. **[Immediate actions if threats found]**
2. **[Security improvements to consider]**
3. **[Monitoring suggestions]**

## Next Steps

- For detailed plist analysis: Read specific files
- For VirusTotal scanning: `/mac-cleaner:security-scan --deep`
- For ongoing protection: Consider tools like KnockKnock, BlockBlock

---

**Note**: This scan checks common malware indicators but is not a replacement for dedicated antivirus software. For critical systems, consider professional security assessment.
```

## Known Safe LaunchAgents

Do not flag these as suspicious:
- `com.apple.*` - Apple system services
- `com.google.*` - Google applications
- `com.microsoft.*` - Microsoft applications
- `com.docker.*` - Docker Desktop
- `homebrew.*` - Homebrew services
- `com.adobe.*` - Adobe applications
- `com.spotify.*` - Spotify
- `com.dropbox.*` - Dropbox

## Risk Classification

| Level | Criteria |
|-------|----------|
| CRITICAL | Known malware signatures, SIP disabled, active threats |
| HIGH | Unsigned plist from suspicious location, processes from /tmp |
| MEDIUM | Unknown unsigned LaunchAgent, many browser extensions |
| LOW | Old unused LaunchAgents, minor config issues |
| CLEAN | No suspicious indicators found |

## Safety Notes

1. **Never auto-remove** anything - only report
2. **Explain findings clearly** - help user understand risk
3. **Recommend professional help** for critical findings
4. **Keep user calm** - many findings are false positives
