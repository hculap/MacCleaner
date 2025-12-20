---
description: Analyze RAM usage, memory pressure, and identify memory-heavy processes
allowed-tools: Bash
argument-hint:
---

# Memory Audit Command

Generate a comprehensive memory usage report: $ARGUMENTS

This command takes no arguments. It performs a full memory analysis.

## Step 1: Memory Pressure Status

```bash
echo "=== Memory Pressure ==="
memory_pressure 2>/dev/null || echo "memory_pressure command not available"
```

Interpret result:
- **System-wide memory status: OK** - Healthy
- **System-wide memory status: WARN** - Under pressure
- **System-wide memory status: CRITICAL** - Needs attention

## Step 2: Detailed Memory Statistics

```bash
echo "=== Memory Statistics ==="
vm_stat

# Parse into readable format
echo ""
echo "=== Memory Breakdown (GB) ==="
vm_stat | perl -ne '/page size of (\d+)/ and $size=$1; /Pages (free|active|inactive|speculative|wired down|occupied by compressor|purgeable):\s+(\d+)/ and printf "%-25s: %6.2f GB\n", $1, $2*$size/1073741824'
```

## Step 3: Total RAM

```bash
echo "=== System Memory ==="
sysctl -n hw.memsize | awk '{print "Total RAM: " $1/1024/1024/1024 " GB"}'
```

## Step 4: Top Memory-Consuming Processes

```bash
echo "=== Top 15 Memory Consumers ==="
ps aux -m | head -16 | tail -15 | awk '{printf "%-6s %-8s %-45s\n", $4"%", $6/1024"MB", substr($11,1,45)}'
```

## Step 5: Swap Usage

```bash
echo "=== Swap Usage ==="
sysctl vm.swapusage 2>/dev/null || echo "Swap info not available"
```

## Step 6: Memory-Heavy Application Categories

Check for known memory hogs:

```bash
echo "=== Application Memory Usage ==="

# Browsers
ps aux | grep -i "chrome\|safari\|firefox" | grep -v grep | awk '{sum+=$6} END {print "Browsers: " sum/1024 " MB"}'

# Electron apps
ps aux | grep -i "electron\|slack\|discord\|vscode\|code helper" | grep -v grep | awk '{sum+=$6} END {print "Electron Apps: " sum/1024 " MB"}'

# Docker
ps aux | grep -i "docker\|com.docker" | grep -v grep | awk '{sum+=$6} END {print "Docker: " sum/1024 " MB"}'

# IDEs
ps aux | grep -i "xcode\|intellij\|pycharm\|webstorm" | grep -v grep | awk '{sum+=$6} END {print "IDEs: " sum/1024 " MB"}'
```

## Step 7: Apple Silicon Check

```bash
echo "=== System Architecture ==="
uname -m
# arm64 = Apple Silicon
# x86_64 = Intel
```

## Report Generation

```markdown
# Memory Audit Report

**Generated**: [timestamp]

## Overview

| Metric | Value |
|--------|-------|
| **Memory Pressure** | [OK / WARN / CRITICAL] |
| **Total RAM** | X GB |
| **Available** | ~X GB |
| **Swap Used** | X MB |

## Memory Breakdown

| Type | Size | Description |
|------|------|-------------|
| Wired | X.XX GB | Locked in RAM, cannot be freed |
| Active | X.XX GB | Currently in use by applications |
| Inactive | X.XX GB | Recently used, can be reclaimed |
| Compressed | X.XX GB | Compressed to save space |
| Free | X.XX GB | Immediately available |
| Purgeable | X.XX GB | Can be freed instantly |

## Top Memory Consumers

| Memory % | Size | Process |
|----------|------|---------|
| X% | X MB | process_name |
| ... | ... | ... |

## Application Categories

| Category | Memory Used |
|----------|-------------|
| Browsers | X MB |
| Electron Apps | X MB |
| Docker | X MB |
| IDEs | X MB |

## Swap Analysis

- **Swap Used**: X MB of X MB
- **Interpretation**: [Normal / Elevated / High]

## System Notes

- **Architecture**: [Apple Silicon / Intel]
- **Unified Memory**: [Yes (Apple Silicon) / No (Intel)]

## Recommendations

Based on findings:

1. **[Primary recommendation]**
2. **[Secondary recommendation]**
3. **[Optional suggestion]**

---

### Quick Actions

| Action | Command |
|--------|---------|
| Free inactive memory | `/mac-cleaner:memory-clean --purge` |
| Kill specific app | `/mac-cleaner:memory-clean --kill <app>` |
| Close browser tabs | Consider using tab suspender |
```

## Understanding macOS Memory

Provide context to user:

### Memory Pressure is the Key Metric

- **OK (Green)**: System is healthy, plenty of headroom
- **WARN (Yellow)**: System is coping but working harder
- **CRITICAL (Red)**: System is struggling, take action

### "High Memory Usage" is Normal

macOS intentionally uses available RAM for caching. This improves performance.
The important question is: "Is the system under memory pressure?"

### Inactive Memory is Not Wasted

Inactive memory contains recently-used data that can be:
- Reclaimed instantly if an app needs it
- Reused quickly if the same app accesses it again

### When to Take Action

- Memory pressure is WARN or CRITICAL
- Swap usage is very high (>2GB on 16GB system)
- Apps are getting killed unexpectedly
- System is noticeably slow
