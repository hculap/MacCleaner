---
description: Full disk usage audit with detailed report showing space consumers and cleanup opportunities
allowed-tools: Bash, Read, Write, Glob
argument-hint: [--output <path>]
---

# Disk Audit Command

Generate a comprehensive disk usage report: $ARGUMENTS

## Step 1: Parse Arguments

Parse `$ARGUMENTS` to extract:
- `--output <path>`: Save report to specified file path (optional)

## Step 2: Overall Disk Status

```bash
# Get disk usage summary
df -h / | tail -1 | awk '{print "Total: "$2", Used: "$3", Available: "$4", Usage: "$5}'
```

## Step 3: Home Directory Breakdown

```bash
# Major directories in home
echo "=== Home Directory Breakdown ==="
du -sh ~/Desktop ~/Documents ~/Downloads ~/Movies ~/Music ~/Pictures ~/Library 2>/dev/null | sort -hr
```

## Step 4: Developer Directories

```bash
# Developer-related directories
echo "=== Developer Directories ==="
du -sh ~/Library/Developer 2>/dev/null
du -sh ~/Library/Developer/Xcode/DerivedData 2>/dev/null
du -sh ~/Library/Developer/Xcode/Archives 2>/dev/null
du -sh ~/Library/Developer/CoreSimulator 2>/dev/null

# Package manager caches
echo "=== Package Manager Caches ==="
du -sh ~/.npm 2>/dev/null
du -sh ~/.yarn 2>/dev/null
du -sh ~/.pnpm-store 2>/dev/null
du -sh ~/.cargo 2>/dev/null
du -sh ~/.rustup 2>/dev/null
du -sh ~/.gradle 2>/dev/null
du -sh ~/.m2 2>/dev/null
```

## Step 5: Docker Usage

```bash
# Docker Desktop data
echo "=== Docker ==="
du -sh ~/Library/Containers/com.docker.docker 2>/dev/null || echo "Docker not installed"

# If Docker is running, get more details
docker system df 2>/dev/null || true
```

## Step 6: System Caches and Logs

```bash
echo "=== Caches & Logs ==="
du -sh ~/Library/Caches 2>/dev/null
du -sh ~/Library/Logs 2>/dev/null
du -sh /Library/Caches 2>/dev/null
```

## Step 7: Trash

```bash
echo "=== Trash ==="
du -sh ~/.Trash 2>/dev/null
```

## Step 8: Homebrew

```bash
echo "=== Homebrew ==="
brew --cache 2>/dev/null && du -sh $(brew --cache) 2>/dev/null || echo "Homebrew not installed"
```

## Step 9: Find Large Files

```bash
echo "=== Top 20 Largest Files (>100MB) ==="
find ~ -type f -size +100M -exec ls -lh {} \; 2>/dev/null | awk '{print $5, $9}' | sort -hr | head -20
```

## Step 10: Find Old Downloads

```bash
echo "=== Downloads Older Than 30 Days ==="
find ~/Downloads -type f -mtime +30 -exec ls -lh {} \; 2>/dev/null | awk '{sum+=$5} END {print "Total: " sum/1024/1024 " MB"}' | head -1
find ~/Downloads -type f -mtime +30 2>/dev/null | wc -l | xargs echo "Files:"
```

## Step 11: node_modules Directories

```bash
echo "=== node_modules Directories ==="
find ~ -name "node_modules" -type d -prune 2>/dev/null | while read dir; do
  du -sh "$dir" 2>/dev/null
done | sort -hr | head -10
```

## Report Generation

Compile findings into a structured report:

```markdown
# Disk Usage Audit Report

**Generated**: [timestamp]
**Machine**: [hostname]

## Summary

| Metric | Value |
|--------|-------|
| Total Disk | X GB |
| Used | X GB (X%) |
| Available | X GB |

## Space Breakdown

### User Directories
| Directory | Size |
|-----------|------|
| Documents | X GB |
| Downloads | X GB |
| ... | ... |

### Developer Tools
| Category | Size |
|----------|------|
| Xcode DerivedData | X GB |
| iOS Simulators | X GB |
| npm cache | X GB |
| ... | ... |

### Cleanup Opportunities

| Category | Size | Safety | Action |
|----------|------|--------|--------|
| User Caches | X GB | Safe | `~/Library/Caches/*` |
| Trash | X GB | Safe | Empty Trash |
| Xcode DerivedData | X GB | Safe | Delete DerivedData |
| Docker | X GB | Confirm | `docker system prune -a` |
| Old Downloads | X GB | Review | Check files first |
| node_modules | X GB | Confirm | Remove unused projects |

### Large Files
| Size | Path |
|------|------|
| X GB | /path/to/file |
| ... | ... |

## Recommendations

1. **Quick Win**: [Easiest high-impact cleanup]
2. **Moderate Effort**: [Requires some review]
3. **Consider**: [Optional optimizations]

**Estimated Recoverable Space**: X GB

---
Run `/mac-cleaner:disk-clean` to perform cleanup.
```

## Output Handling

- If `--output` specified: Write report to file using Write tool
- Always display summary to user
- Format sizes in human-readable units (GB, MB)
- Sort all lists by size (largest first)
