---
description: Interactive disk cleanup for caches, Docker, builds, large files, old files, and Homebrew
allowed-tools: Bash, Read, AskUserQuestion, Glob
argument-hint: [--type <cache|docker|builds|large|old|homebrew|all>]
---

# Disk Clean Command

Perform interactive disk cleanup: $ARGUMENTS

## Step 1: Parse Arguments

Parse `$ARGUMENTS` to extract:
- `--type <type>`: Clean specific category only
  - `cache` - System and app caches
  - `docker` - Docker images, containers, volumes
  - `builds` - Build artifacts (DerivedData, node_modules, target/)
  - `large` - Large files (>100MB)
  - `old` - Old files (>6 months)
  - `homebrew` - Homebrew cache and outdated packages
  - `all` - Run all cleanup types interactively

## Step 2: Read User Settings

Check for `.claude/mac-cleaner.local.md` and read preferences:
- `large_file_threshold_mb` (default: 100)
- `old_file_days` (default: 180)
- `auto_confirm_cache_cleanup` (default: false)
- `excluded_paths` (default: empty)

## Step 3: Current Disk Status

```bash
df -h / | tail -1 | awk '{print "Disk: "$3" used of "$2" ("$5" full), "$4" available"}'
```

## Step 4: Determine Cleanup Categories

If `--type` specified, run only that category. Otherwise, present menu:

Use AskUserQuestion to ask:
- Question: "Which cleanup categories would you like to run?"
- Options (multiSelect: true):
  - "Caches (system, app, browser)"
  - "Docker (images, containers, volumes)"
  - "Build artifacts (Xcode, node_modules, etc.)"
  - "Large files (>100MB)"
  - "Old files (>6 months)"
  - "Homebrew (cache, outdated packages)"

## Step 5: Execute Selected Categories

For each selected category, follow the specific cleanup procedure.

---

## Category: Caches

### Scan
```bash
echo "=== Cache Sizes ==="
du -sh ~/Library/Caches 2>/dev/null
du -sh /Library/Caches 2>/dev/null
du -sh ~/Library/Caches/com.apple.Safari 2>/dev/null
du -sh ~/Library/Caches/Google/Chrome 2>/dev/null
du -sh ~/Library/Caches/Firefox 2>/dev/null
```

### Confirm and Clean
```bash
# User caches (safe)
rm -rf ~/Library/Caches/* 2>/dev/null

# System caches (safe but requires explaining)
sudo rm -rf /Library/Caches/* 2>/dev/null

# DNS cache
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

**Ask before cleaning**: Show size and confirm "Clean X GB of cache files?"

---

## Category: Docker

### Scan
```bash
# Check if Docker is running
docker info >/dev/null 2>&1 || echo "Docker not running"

# Show current usage
docker system df 2>/dev/null
```

### Confirm and Clean

Ask user which Docker cleanup level:
- "Remove unused images only" → `docker image prune -f`
- "Remove all unused resources" → `docker system prune -f`
- "Remove everything including volumes (CAUTION)" → `docker system prune -a --volumes -f`
- "Also clean build cache" → `docker builder prune -f`

```bash
# Based on selection
docker image prune -f
docker system prune -f
docker system prune -a --volumes -f
docker builder prune -f
```

---

## Category: Build Artifacts

### Scan
```bash
echo "=== Build Artifacts ==="
du -sh ~/Library/Developer/Xcode/DerivedData 2>/dev/null
du -sh ~/Library/Developer/Xcode/Archives 2>/dev/null
du -sh ~/Library/Developer/CoreSimulator/Devices 2>/dev/null

# Find node_modules
echo "=== node_modules Directories ==="
find ~ -name "node_modules" -type d -prune 2>/dev/null | head -20 | while read d; do du -sh "$d"; done | sort -hr

# Find Rust target directories
echo "=== Rust target/ Directories ==="
find ~ -name "target" -type d -path "*/target" 2>/dev/null | head -10 | while read d; do du -sh "$d"; done | sort -hr

# Find Python venvs
echo "=== Python venv Directories ==="
find ~ -name "venv" -o -name ".venv" -type d 2>/dev/null | head -10 | while read d; do du -sh "$d"; done | sort -hr
```

### Confirm and Clean

Present checklist of found artifacts and let user select which to remove.

```bash
# Xcode DerivedData
rm -rf ~/Library/Developer/Xcode/DerivedData/*

# Xcode Archives (ask separately - user might want to keep some)
rm -rf ~/Library/Developer/Xcode/Archives/*

# iOS Simulators (unavailable ones only)
xcrun simctl delete unavailable 2>/dev/null

# node_modules (for each selected)
rm -rf <path_to_node_modules>

# Rust target (for each selected)
rm -rf <path_to_target>
```

---

## Category: Large Files

### Scan
```bash
echo "=== Files Larger Than ${threshold}MB ==="
find ~ -type f -size +${threshold}M -exec ls -lh {} \; 2>/dev/null | awk '{print $5, $9}' | sort -hr | head -30
```

### Review and Clean

Present list of large files and ask which to delete.

**Important**: Never auto-delete large files. Always ask user to confirm each file or batch.

Show file details:
- Name and path
- Size
- Last modified date
- File type (if recognizable)

---

## Category: Old Files

### Scan
```bash
echo "=== Files Not Modified in ${days} Days ==="
find ~/Downloads -type f -mtime +${days} 2>/dev/null | head -30
find ~/Desktop -type f -mtime +${days} 2>/dev/null | head -30
```

### Review and Clean

Present list and ask user which to delete or move to Trash.

---

## Category: Homebrew

### Scan
```bash
echo "=== Homebrew Status ==="
brew --cache 2>/dev/null && du -sh $(brew --cache) 2>/dev/null
brew outdated 2>/dev/null | head -10
brew autoremove --dry-run 2>/dev/null
```

### Confirm and Clean

```bash
# Clean cache
brew cleanup -s

# Remove orphaned dependencies
brew autoremove

# Show what was cleaned
brew cleanup --dry-run
```

---

## Final Report

After all selected cleanups complete:

```markdown
## Cleanup Complete

### Actions Performed
| Category | Space Freed |
|----------|-------------|
| Caches | X GB |
| Docker | X GB |
| ... | ... |

### Disk Status
- **Before**: X GB available
- **After**: X GB available
- **Recovered**: X GB

### Notes
- [Any warnings or items that need attention]
```

## Safety Guidelines

1. **Always confirm before deleting**
2. **Skip excluded paths** from user settings
3. **Never delete without showing what will be deleted**
4. **Prefer moving to Trash** over permanent deletion for user files
5. **Show progress** during long operations
