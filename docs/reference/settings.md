# Settings Reference

Complete reference for MacCleaner configuration options.

## Configuration File

Create `.claude/mac-cleaner.local.md` in your project or home directory:

```yaml
---
large_file_threshold_mb: 100
old_file_days: 180
auto_confirm_cache_cleanup: false
excluded_paths:
  - ~/Documents/Important
virustotal_enabled: false
---

# MacCleaner Settings

Optional markdown content below the frontmatter.
```

## Available Settings

### large_file_threshold_mb

**Type:** Integer
**Default:** `100`
**Unit:** Megabytes

Minimum file size to be flagged as a "large file" during audits and cleanup.

```yaml
large_file_threshold_mb: 100   # Flag files over 100 MB
large_file_threshold_mb: 500   # Only flag files over 500 MB
large_file_threshold_mb: 50    # More aggressive, flag files over 50 MB
```

**Use cases:**
- Increase if you have many legitimate large files
- Decrease if you want to catch more files for review

---

### old_file_days

**Type:** Integer
**Default:** `180`
**Unit:** Days

Age threshold for files to be considered "old" in Downloads/Desktop scanning.

```yaml
old_file_days: 180   # 6 months (default)
old_file_days: 365   # 1 year - more conservative
old_file_days: 90    # 3 months - more aggressive
old_file_days: 30    # 1 month - very aggressive
```

**Affects:**
- `disk-audit` old files detection
- `disk-clean --type old` file selection

---

### auto_confirm_cache_cleanup

**Type:** Boolean
**Default:** `false`

Skip confirmation prompts for cache cleanup operations.

```yaml
auto_confirm_cache_cleanup: false  # Always ask (default)
auto_confirm_cache_cleanup: true   # Clean caches without asking
```

**When to enable:**
- You run cache cleanup frequently
- You understand what gets cleaned
- You want faster automated maintenance

**Applies to:**
- User caches (`~/Library/Caches/`)
- System caches (`/Library/Caches/`)
- Browser caches

Does NOT affect:
- Docker cleanup
- Build artifacts
- Large/old files
- Any user data

---

### excluded_paths

**Type:** Array of strings
**Default:** `[]` (empty)

Paths to skip during all cleanup operations.

```yaml
excluded_paths:
  - ~/Documents/Important
  - ~/Projects/active-project
  - ~/Library/Application Support/CriticalApp
  - ~/Downloads/Keep-These
```

**Path formats supported:**
- `~` expands to home directory
- Absolute paths work
- Directory paths exclude the entire tree

**Examples:**

Protect a critical project:
```yaml
excluded_paths:
  - ~/Projects/production-app
```

Keep certain downloads:
```yaml
excluded_paths:
  - ~/Downloads/ISOs
  - ~/Downloads/Archives
```

Protect app data:
```yaml
excluded_paths:
  - ~/Library/Application Support/MyApp
```

---

### virustotal_enabled

**Type:** Boolean
**Default:** `false`

Enable VirusTotal integration for deep security scans.

```yaml
virustotal_enabled: false  # Standard scanning only (default)
virustotal_enabled: true   # Enable hash-based malware checking
```

**Requirements when enabled:**
1. VirusTotal API key
2. Environment variable: `VIRUSTOTAL_API_KEY`

**How to set up:**

1. Get free API key from [VirusTotal](https://www.virustotal.com/gui/join-us)

2. Set environment variable:
   ```bash
   # In ~/.zshrc or ~/.bashrc
   export VIRUSTOTAL_API_KEY="your-api-key-here"
   ```

3. Enable in settings:
   ```yaml
   virustotal_enabled: true
   ```

4. Use deep scan:
   ```
   /mac-cleaner:security-scan --deep
   ```

**Rate limits:**
- Free API: 4 requests/minute, 500/day
- Deep scans may hit limits with many suspicious files

---

## Full Example Configuration

```yaml
---
# Disk cleanup settings
large_file_threshold_mb: 250
old_file_days: 365
auto_confirm_cache_cleanup: true

# Protected paths
excluded_paths:
  - ~/Documents
  - ~/Desktop
  - ~/Projects/work
  - ~/Downloads/Important

# Security settings
virustotal_enabled: false
---

# My MacCleaner Configuration

## Notes
- Conservative settings for work machine
- Protecting all user document areas
- Cache cleanup runs without prompts
```

---

## Configuration Precedence

Settings are loaded from:
1. Project-level: `.claude/mac-cleaner.local.md`
2. Built-in defaults

If no configuration file exists, defaults are used.

---

## Validating Configuration

Invalid settings are silently ignored and defaults used. To verify your config is read:

1. Check the audit output mentions your thresholds
2. Verify excluded paths are skipped
3. Run `security-scan --deep` to test VirusTotal (if enabled)

---

## Default Values Summary

| Setting | Default Value |
|---------|---------------|
| `large_file_threshold_mb` | 100 |
| `old_file_days` | 180 |
| `auto_confirm_cache_cleanup` | false |
| `excluded_paths` | [] |
| `virustotal_enabled` | false |
