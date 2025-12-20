# Tutorial: Running a Security Scan

This tutorial walks you through scanning your Mac for malware, suspicious files, and security threats using MacCleaner's security scan feature.

## What You'll Learn

- How to run a security scan
- How to interpret the results
- What different risk levels mean
- What to do if threats are found

## Prerequisites

- MacCleaner plugin installed
- 10-15 minutes

## Step 1: Run a Basic Security Scan

Start with a standard scan:

```
/mac-cleaner:security-scan
```

The scan checks multiple areas of your system and takes about a minute to complete.

## Step 2: Understanding the Report

### System Security Status

The report starts with your system's security posture:

```
### System Security Status
| Check                        | Status  |
|------------------------------|---------|
| System Integrity Protection  | Enabled |
| Gatekeeper                   | Enabled |
```

**Both should be Enabled.** If either is disabled:
- SIP disabled = Major security risk
- Gatekeeper disabled = Apps from anywhere can run

### Overall Risk Assessment

```
### Overall Risk Assessment
**Risk Level**: CLEAN
```

Risk levels:
| Level | Meaning |
|-------|---------|
| CLEAN | No suspicious indicators found |
| LOW | Minor issues, not urgent |
| MEDIUM | Some concerns, review recommended |
| HIGH | Suspicious items found, investigate |
| CRITICAL | Likely threats, take action immediately |

## Step 3: Review LaunchAgents

LaunchAgents are the most common place for Mac malware to hide.

```
### User LaunchAgents (~Library/LaunchAgents/)
| File                           | Status     | Analysis        |
|--------------------------------|------------|-----------------|
| com.google.keystone.agent.plist | OK        | Google Update   |
| com.spotify.webhelper.plist    | OK        | Spotify         |
| com.docker.helper.plist        | OK        | Docker Desktop  |
```

**What to look for:**

| Status | Meaning |
|--------|---------|
| OK | Known legitimate software |
| SUSPICIOUS | Unknown or unusual, needs investigation |
| MALWARE | Matches known malware patterns |

**Red flags in LaunchAgents:**
- Random-looking names like `asjdfklsj.plist`
- Files pointing to `/tmp/` or `/Users/Shared/`
- Encoded or obfuscated commands
- Files you don't recognize

## Step 4: Check Staging Locations

Malware often hides in temporary locations:

```
### Suspicious Locations Checked
| Location         | Finding      |
|------------------|--------------|
| /Users/Shared/   | Clean        |
| /private/tmp/    | Clean        |
| Hidden home dirs | Clean        |
```

If items are found here, review them carefully.

## Step 5: Review Running Processes

```
### Process Analysis
- Suspicious processes from temp locations: 0
- Unusual root processes: 0
```

Processes running from `/tmp/` or `/private/tmp/` are almost always suspicious.

## Step 6: Interpreting Common Findings

### "Unknown LaunchAgent"

A LaunchAgent that isn't in the known-safe list.

**What to do:**
1. Look at the file name—does it match software you installed?
2. Check the program path inside the plist
3. Search online for the plist name
4. If unsure, don't delete it—investigate first

### "Process running from /tmp"

Legitimate software rarely runs from temporary directories.

**What to do:**
1. Note the process name
2. Check if it matches running applications
3. Search online for the process name
4. Consider terminating if suspicious

### "SIP Disabled"

System Integrity Protection is off—this is a significant risk.

**What to do:**
1. If you disabled it intentionally (e.g., for development), consider re-enabling
2. If you didn't disable it, your system may be compromised
3. Re-enable: Restart → Hold Cmd+R → Terminal → `csrutil enable`

## Step 7: Run a Deep Scan (Optional)

For enhanced malware detection, use VirusTotal integration:

```
/mac-cleaner:security-scan --deep
```

This:
1. Hashes suspicious files
2. Checks them against VirusTotal's database
3. Reports if multiple antivirus engines detect them

**Requires:** VirusTotal API key (see [setup guide](../how-to/setup-virustotal.md))

## What If Threats Are Found?

### LOW Risk Items

Usually safe to ignore, but review:
- Old LaunchAgents from uninstalled apps → Can delete
- Many browser extensions → Review and remove unused ones

### MEDIUM Risk Items

Investigate before acting:
1. Research the file/process name online
2. Check when it was created
3. Look for related recently-installed software
4. If suspicious, consider removal

### HIGH Risk Items

Take these seriously:
1. Don't click or run suspicious files
2. Document what was found
3. Remove the LaunchAgent/file
4. Consider professional help if unsure

### CRITICAL Findings

Immediate action needed:
1. Disconnect from the internet
2. Don't enter any passwords
3. Document all findings
4. Consider professional malware removal
5. Change passwords from another device

## Removing Suspicious Items

### Removing a LaunchAgent

1. Unload it first:
   ```bash
   launchctl unload ~/Library/LaunchAgents/<filename>.plist
   ```

2. Delete the plist file:
   ```bash
   rm ~/Library/LaunchAgents/<filename>.plist
   ```

3. Find and delete the actual malware (path is in the plist)

### Killing a Suspicious Process

```bash
pkill -f "process_name"
```

Or use Activity Monitor to force quit.

## Preventing Future Infections

1. **Keep macOS updated** - Apple patches security issues regularly
2. **Leave SIP enabled** - Don't disable it unless absolutely necessary
3. **Leave Gatekeeper enabled** - Only run signed applications
4. **Be careful with downloads** - Only download from trusted sources
5. **Don't install pirated software** - Common malware vector
6. **Review what you install** - Don't click through installation blindly

## Regular Security Maintenance

Run a security scan:
- **Monthly** for most users
- **After installing new software** from unfamiliar sources
- **If you notice suspicious behavior** (popups, redirects, slowdowns)
- **After connecting to untrusted networks**

## Next Steps

- **[Understanding security scans](../explanations/security-scanning.md)** - Deeper explanation
- **[Set up VirusTotal](../how-to/setup-virustotal.md)** - Enable deep scanning
- **[Troubleshooting](../how-to/troubleshooting.md)** - When things go wrong

## When to Seek Professional Help

Contact a security professional if:
- CRITICAL threats are found
- You're unsure what to do
- Threats return after removal
- Sensitive data may be compromised
- You handle confidential business/client data
