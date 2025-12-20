# Troubleshooting Guide

Solutions to common issues when using MacCleaner.

## Command Issues

### "Command not found"

**Symptom:** MacCleaner commands don't work.

**Solutions:**

1. **Check plugin is installed:**
   ```bash
   claude plugins list
   ```
   Look for `mac-cleaner`.

2. **Reinstall the plugin:**
   ```bash
   claude plugins install mac-cleaner
   ```

3. **For local development:**
   ```bash
   claude --plugin-dir /path/to/MacCleaner
   ```

### "Permission denied"

**Symptom:** Commands fail with permission errors.

**Solutions:**

1. **For cache cleanup:**
   System caches require sudo. MacCleaner will prompt for password.

2. **For purge command:**
   ```bash
   sudo purge
   ```
   Enter your admin password.

3. **Fix Homebrew permissions:**
   ```bash
   sudo chown -R $(whoami) $(brew --prefix)/*
   ```

### Command hangs or times out

**Symptom:** Command runs but never completes.

**Solutions:**

1. **Large home directory:** Scans take longer with more files. Wait or reduce scope.

2. **Spinning disk:** SSDs are much faster. Consider upgrading.

3. **Broken symlinks:** Check for broken links:
   ```bash
   find ~ -type l ! -exec test -e {} \; -print 2>/dev/null | head -20
   ```

## Disk Cleanup Issues

### "Cannot delete file"

**Symptom:** File deletion fails.

**Solutions:**

1. **File in use:**
   Close the application using the file.

2. **Permission denied:**
   ```bash
   sudo rm -rf /path/to/file
   ```

3. **System Integrity Protection:**
   Some system files cannot be deleted even with sudo.

### DerivedData won't delete

**Symptom:** Xcode build files fail to delete.

**Solutions:**

1. **Close Xcode first:**
   Quit Xcode completely, then try again.

2. **Manual deletion:**
   ```bash
   rm -rf ~/Library/Developer/Xcode/DerivedData/*
   ```

3. **Files locked:**
   ```bash
   chflags -R nouchg ~/Library/Developer/Xcode/DerivedData
   rm -rf ~/Library/Developer/Xcode/DerivedData/*
   ```

### Trash won't empty

**Symptom:** Can't empty Trash.

**Solutions:**

1. **Close apps using files in Trash**

2. **Force empty:**
   ```bash
   sudo rm -rf ~/.Trash/*
   ```

3. **Fix permissions:**
   ```bash
   sudo chown -R $(whoami) ~/.Trash
   rm -rf ~/.Trash/*
   ```

## Docker Issues

### "Docker not running"

**Symptom:** Docker commands fail.

**Solutions:**

1. **Start Docker Desktop:**
   Open Docker Desktop from Applications.

2. **Wait for startup:**
   Docker takes 30-60 seconds to fully start.

3. **Check Docker status:**
   ```bash
   docker info
   ```

### Docker cleanup doesn't free disk space

**Symptom:** `docker system prune` runs but disk usage unchanged.

**Solutions:**

Docker's disk image doesn't automatically shrink.

1. **Reduce disk limit:**
   Docker Desktop → Preferences → Resources → Disk image size → Apply

2. **Reset Docker:**
   Docker Desktop → Troubleshoot → Clean / Purge data

3. **Delete disk image manually:**
   ```bash
   # Stop Docker first
   rm ~/Library/Containers/com.docker.docker/Data/vms/0/data/Docker.raw
   ```
   Restart Docker—it creates a new empty image.

## Memory Issues

### "purge" command fails

**Symptom:** Memory purge doesn't work.

**Solutions:**

1. **Use sudo:**
   ```bash
   sudo purge
   ```

2. **Check command exists:**
   ```bash
   which purge
   ```
   Should be `/usr/sbin/purge`.

### Memory pressure stays high after cleanup

**Symptom:** RAM pressure doesn't improve.

**Solutions:**

1. **Close memory-hungry apps:**
   Check Activity Monitor → Memory → Sort by Memory

2. **Restart the Mac:**
   Sometimes necessary to fully clear memory.

3. **Check for memory leaks:**
   Apps that grow continuously have leaks. Force quit and reopen.

## Security Scan Issues

### Scan takes very long

**Symptom:** Security scan runs for many minutes.

**Solutions:**

1. **Large LaunchAgents folder:**
   Normal if you have many apps installed.

2. **Slow disk:**
   SSDs scan faster than spinning drives.

3. **Run during off-hours:**
   Let it complete without interrupting.

### False positives

**Symptom:** Legitimate software flagged as suspicious.

**Solutions:**

1. **Check the file name:**
   Many legitimate apps use LaunchAgents.

2. **Known safe list:**
   MacCleaner skips known-good agents like:
   - `com.apple.*`
   - `com.google.*`
   - `com.microsoft.*`
   - `com.docker.*`

3. **Research online:**
   Search for the plist name to verify.

### VirusTotal deep scan fails

**Symptom:** `--deep` flag doesn't work.

**Solutions:**

1. **Check API key:**
   ```bash
   echo $VIRUSTOTAL_API_KEY
   ```
   Should show your key.

2. **Set the key:**
   ```bash
   export VIRUSTOTAL_API_KEY="your-key-here"
   ```
   Add to `~/.zshrc` for persistence.

3. **API rate limit:**
   Free tier: 4 requests/minute. Wait and retry.

## Configuration Issues

### Settings not being applied

**Symptom:** Custom settings in `.claude/mac-cleaner.local.md` ignored.

**Solutions:**

1. **Check file location:**
   File must be at `.claude/mac-cleaner.local.md` in project root.

2. **Check YAML syntax:**
   ```yaml
   ---
   large_file_threshold_mb: 100
   ---
   ```
   Frontmatter must be valid YAML between `---` markers.

3. **Verify values:**
   - Numbers without quotes
   - Booleans: `true`/`false` (lowercase)
   - Arrays: Use `- item` format

### Excluded paths still scanned

**Symptom:** Paths in `excluded_paths` are still checked.

**Solutions:**

1. **Use correct path format:**
   ```yaml
   excluded_paths:
     - ~/Documents/Important
   ```
   Use `~` for home directory.

2. **Check for typos:**
   Paths must match exactly.

3. **Use absolute paths as fallback:**
   ```yaml
   excluded_paths:
     - /Users/yourname/Documents/Important
   ```

## Getting More Help

If you're still stuck:

1. **Check macOS version:**
   MacCleaner requires macOS 12+.

2. **Try a restart:**
   Many issues resolve after restarting Claude Code or your Mac.

3. **Check system logs:**
   ```bash
   log show --last 5m --predicate 'process == "claude"'
   ```

4. **Report an issue:**
   If it's a bug, report at the MacCleaner repository.
