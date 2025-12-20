# How to Set Up VirusTotal Integration

Enable deep malware scanning by connecting MacCleaner to VirusTotal's database of 70+ antivirus engines.

## What VirusTotal Adds

Without VirusTotal:
- MacCleaner checks for known malware patterns
- Analyzes LaunchAgents for suspicious characteristics
- Flags obviously malicious indicators

With VirusTotal:
- Suspicious files are hashed and checked
- 70+ antivirus engines analyze the hash
- Get detection counts and threat names
- Higher confidence in threat identification

## Step 1: Get a Free API Key

1. Go to [VirusTotal](https://www.virustotal.com/gui/join-us)

2. Create a free account:
   - Enter email
   - Create password
   - Verify email

3. After login, go to your profile:
   - Click your avatar → API key
   - Or visit: https://www.virustotal.com/gui/my-apikey

4. Copy your API key (looks like: `abc123def456...`)

## Step 2: Set the Environment Variable

### For Current Session (Temporary)

```bash
export VIRUSTOTAL_API_KEY="your-api-key-here"
```

This only lasts until you close the terminal.

### For Permanent Use

Add to your shell configuration:

**For zsh (default on modern macOS):**
```bash
echo 'export VIRUSTOTAL_API_KEY="your-api-key-here"' >> ~/.zshrc
source ~/.zshrc
```

**For bash:**
```bash
echo 'export VIRUSTOTAL_API_KEY="your-api-key-here"' >> ~/.bash_profile
source ~/.bash_profile
```

### Verify It's Set

```bash
echo $VIRUSTOTAL_API_KEY
```

Should show your API key.

## Step 3: Enable in MacCleaner Settings

Create or edit `.claude/mac-cleaner.local.md`:

```yaml
---
virustotal_enabled: true
---
```

## Step 4: Run a Deep Scan

```
/mac-cleaner:security-scan --deep
```

The scan will:
1. Perform standard security checks
2. Hash any suspicious files found
3. Query VirusTotal for each hash
4. Report detection results

## Understanding Results

### No Detections

```
File: suspicious-file.plist
Hash: abc123...
VirusTotal: 0/70 engines detected
Result: CLEAN
```

No antivirus engines flagged this file. Likely safe, but not guaranteed.

### Some Detections

```
File: possibly-malicious.plist
Hash: def456...
VirusTotal: 3/70 engines detected
Detections: Generic.Trojan, Suspicious.Gen
Result: SUSPICIOUS
```

A few engines flagged it. Could be:
- False positive
- New/uncommon malware
- Potentially unwanted program (PUP)

Investigate further.

### Many Detections

```
File: definitely-bad.plist
Hash: ghi789...
VirusTotal: 45/70 engines detected
Detections: Trojan.OSX.MacSpy, OSX.BeaverTail
Result: MALWARE
```

High confidence this is malware. Remove immediately.

## Rate Limits

Free VirusTotal API has limits:
- 4 requests per minute
- 500 requests per day
- 15,500 requests per month

For typical security scans, this is plenty. If you hit limits:
- Wait a minute and try again
- Run deep scans less frequently
- Consider VirusTotal Premium for higher limits

## Privacy Considerations

MacCleaner only sends **file hashes** to VirusTotal, not file contents.

A hash is a fingerprint:
- One-way: Can't reconstruct file from hash
- Anonymous: Doesn't reveal your identity
- Private: File contents stay on your machine

This is industry-standard practice for malware checking.

## Troubleshooting

### "VirusTotal API key not found"

The environment variable isn't set. Check:

```bash
echo $VIRUSTOTAL_API_KEY
```

If empty, set it again (Step 2).

### "Rate limit exceeded"

Wait a minute and try again. Free tier allows 4 requests/minute.

### "API request failed"

Check:
1. Internet connection
2. API key is correct (no extra spaces)
3. VirusTotal service status

### Deep scan shows no additional findings

This means:
- No suspicious files to check, or
- Suspicious files have no VirusTotal matches

Standard scan still provides valuable information.

## Best Practices

1. **Don't run deep scans constantly** - Standard scans are usually sufficient

2. **Use deep scan when:**
   - Standard scan finds something suspicious
   - After downloading software from unknown sources
   - When you suspect infection

3. **Keep your API key private** - Don't share or commit to repositories

4. **Premium API is overkill** for personal use - Free tier is plenty

## Summary

| Step | Action |
|------|--------|
| 1 | Create VirusTotal account at virustotal.com |
| 2 | Copy API key from profile |
| 3 | Set environment variable in shell config |
| 4 | Enable in MacCleaner settings |
| 5 | Run `security-scan --deep` |
