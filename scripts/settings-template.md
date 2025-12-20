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

Copy this file to `.claude/mac-cleaner.local.md` in your project to customize MacCleaner behavior.

## Settings Reference

### Disk Cleanup

| Setting | Default | Description |
|---------|---------|-------------|
| `large_file_threshold_mb` | 100 | Minimum file size (MB) for large file detection |
| `old_file_days` | 180 | Days since modification to consider file "old" |
| `auto_confirm_cache_cleanup` | false | Skip confirmation for cache cleanup |
| `excluded_paths` | [] | Paths to skip during cleanup |

### Security

| Setting | Default | Description |
|---------|---------|-------------|
| `virustotal_enabled` | false | Enable VirusTotal integration for deep scans |

## Example Custom Configuration

```yaml
---
large_file_threshold_mb: 500
old_file_days: 365
auto_confirm_cache_cleanup: true
excluded_paths:
  - ~/Documents
  - ~/Desktop
  - ~/Pictures
virustotal_enabled: true
---
```

## Usage

1. Copy this file to your project: `.claude/mac-cleaner.local.md`
2. Modify the YAML frontmatter values
3. MacCleaner commands will read these settings automatically

## Notes

- Settings are per-project (stored in `.claude/` directory)
- Invalid settings are ignored and defaults are used
- Boolean values must be `true` or `false` (lowercase)
- Paths support `~` for home directory
