# Understanding MacCleaner Security Scans

This guide explains what security scans check, how threats work on macOS, and how to interpret scan results.

## How Mac Malware Works

Unlike Windows, macOS has multiple layers of protection. Malware must work around them:

### System Integrity Protection (SIP)

SIP prevents modifications to core system files.

**When enabled:** Malware cannot modify `/System`, `/usr`, or other protected areas.
**When disabled:** System is vulnerable to rootkits and deep infections.

MacCleaner checks: `csrutil status`

**What you want:** SIP enabled.

### Gatekeeper

Controls which applications can run.

**When enabled:** Only signed apps from identified developers can run.
**When disabled:** Any app can run, including unsigned malware.

MacCleaner checks: `spctl --status`

**What you want:** Assessments enabled.

### Notarization

Apple scans apps for malware before distribution.

Apps from the App Store are always notarized. Apps from outside may or may not be.

## Primary Attack Vector: LaunchAgents

The most common way Mac malware persists is through **LaunchAgents**.

### What Are LaunchAgents?

LaunchAgents are plist files that tell macOS to run programs automatically:
- At login
- On a schedule
- When triggered by events

**Legitimate uses:** Auto-updaters, sync services, helper apps
**Malicious uses:** Running malware at startup, maintaining access

### LaunchAgent Locations

| Location | Who Can Write | Risk Level |
|----------|---------------|------------|
| `~/Library/LaunchAgents/` | Current user | HIGH - User malware target |
| `/Library/LaunchAgents/` | Admins only | MEDIUM - System-wide apps |
| `/Library/LaunchDaemons/` | Root only | MEDIUM - System services |
| `/System/Library/Launch*` | SIP protected | LOW - Apple only |

MacCleaner scans the first three locations.

### What Makes a LaunchAgent Suspicious?

**Red flags:**
- Points to programs in `/tmp/`, `/var/tmp/`, `/Users/Shared/`
- Contains Base64 encoded commands
- Has random-looking identifier (`asjkldf.plist`)
- Points to hidden directories (starting with `.`)
- References `curl`, `wget`, `python`, `bash` with obfuscated commands

**Green flags:**
- Recognizable vendor name (`com.google.`, `com.apple.`)
- Points to program in `/Applications/`
- Matches installed software you recognize

## Common Malware Patterns

### Adware

**Purpose:** Show unwanted ads, redirect searches
**Indicators:**
- Browser extensions you didn't install
- LaunchAgents with names like `com.startup.plist`
- Programs that inject ads into webpages

**Risk level:** LOW to MEDIUM (annoying, not dangerous)

### Cryptocurrency Miners

**Purpose:** Use your Mac to mine cryptocurrency
**Indicators:**
- High CPU usage when idle
- Fan running constantly
- Process names mimicking system processes

**Risk level:** MEDIUM (wastes resources, may cause hardware wear)

### Trojans

**Purpose:** Steal data, provide remote access
**Indicators:**
- Keyloggers capturing passwords
- Network connections to unknown servers
- LaunchAgents that run hidden background processes

**Risk level:** HIGH (can steal sensitive data)

### Specific Malware Examples

| Name | Indicator | What It Does |
|------|-----------|--------------|
| BeaverTail | `com.avatar.update.wake.plist` | Steals crypto credentials |
| Shlayer | Fake Flash installers | Drops adware |
| Atomic Stealer | Fake app downloads | Steals passwords, crypto |
| XCSSET | Infected Xcode projects | Steals data, injects code |

## How Scans Work

### Standard Scan

1. **System status check** - SIP, Gatekeeper
2. **LaunchAgent enumeration** - Lists all launch items
3. **Plist analysis** - Reads each plist, checks for red flags
4. **Known patterns** - Matches against malware database
5. **Staging locations** - Checks `/Users/Shared/`, `/tmp/`, etc.
6. **Process check** - Looks for suspicious running processes
7. **Browser extensions** - Counts and lists extensions

### Deep Scan (with VirusTotal)

Standard scan plus:
1. **Hash suspicious files** - SHA-256 of flagged items
2. **Query VirusTotal** - Check against 70+ antivirus engines
3. **Report detections** - Show which engines flagged what

## Interpreting Results

### Risk Levels

| Level | Meaning | Action |
|-------|---------|--------|
| CLEAN | No suspicious indicators | Regular maintenance only |
| LOW | Minor issues, old items | Review when convenient |
| MEDIUM | Unknown items found | Investigate within days |
| HIGH | Suspicious activity | Investigate immediately |
| CRITICAL | Known threats, SIP off | Take action now |

### False Positives

Not every flagged item is malware:

**Common false positives:**
- Legitimate developer tools with unusual paths
- Custom scripts you created
- Enterprise/MDM management software
- Development environments (node, python in unusual locations)

**Before deleting, verify:**
1. Search for the plist name online
2. Check if it matches software you installed
3. Look at the program path—is it legitimate?

### True Positives

Signs something is actually malicious:
- File appeared recently without you installing anything
- Name is random characters
- Online searches show malware reports
- Multiple scan engines flag it (deep scan)

## What MacCleaner Doesn't Detect

MacCleaner is not antivirus software. It doesn't detect:
- Zero-day malware without known patterns
- Malware that doesn't use LaunchAgents
- Memory-resident malware
- Exploits in running applications
- Network-level attacks

For comprehensive protection, consider:
- macOS built-in XProtect (automatic)
- Commercial antivirus for extra coverage
- Regular software updates

## Staying Safe

### Prevention

1. **Keep macOS updated** - Patches security holes
2. **Leave SIP enabled** - Core protection layer
3. **Leave Gatekeeper enabled** - Blocks unsigned apps
4. **Download from trusted sources** - App Store, official sites
5. **Don't install pirated software** - Common infection vector
6. **Be suspicious of "installers"** - Legitimate apps rarely need them

### Detection

1. **Regular scans** - Monthly with MacCleaner
2. **Watch for symptoms** - Slow Mac, strange popups, unexpected behavior
3. **Check Activity Monitor** - Unknown processes using resources
4. **Review LaunchAgents** - After installing new software

### Response

If you find something:
1. **Don't panic** - Many findings are benign
2. **Research first** - Verify it's actually malware
3. **Document** - Note file names, paths, dates
4. **Remove carefully** - Follow proper procedures
5. **Change passwords** - If credentials may be compromised
6. **Monitor** - Watch for return or other signs

## Summary

| Check | Why It Matters |
|-------|----------------|
| SIP | Prevents system file modification |
| Gatekeeper | Blocks unsigned applications |
| LaunchAgents | Primary persistence mechanism |
| Staging locations | Common malware drop zones |
| Running processes | Active threats |

MacCleaner provides a good security baseline but isn't a replacement for careful computing practices and keeping your system updated.
