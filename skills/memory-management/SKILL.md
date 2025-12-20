---
name: Memory Management
description: Use when user asks about "Mac memory", "RAM usage", "slow Mac", "memory pressure", "free RAM", "purge memory", "what's using memory", "application memory", or needs guidance on macOS memory optimization.
version: 0.1.0
allowed-tools: Bash
---

# macOS Memory Management

Understanding and optimizing memory usage on macOS, including the unique way Apple manages RAM.

## How macOS Memory Works

### Key Concept: All RAM Should Be Used

Unlike Windows, macOS intentionally uses all available RAM. Free RAM is considered wasted RAM because it could be caching data to speed up operations.

**What matters is Memory Pressure, not raw usage numbers.**

### Memory Types

| Type | Description | Can Be Freed? |
|------|-------------|---------------|
| **Wired** | Kernel and system-critical data | No - must stay in RAM |
| **Active** | Currently in use by running apps | When app closes |
| **Inactive** | Recently used, kept for quick access | Yes - instantly |
| **Compressed** | Active memory compressed to save space | Decompresses when needed |
| **Free** | Immediately available | N/A |
| **Purgeable** | Can be freed instantly when needed | Yes - instantly |

### Memory Pressure States

| State | Indicator | Meaning | Action |
|-------|-----------|---------|--------|
| **OK** | Green | Healthy, plenty of headroom | None needed |
| **WARN** | Yellow | System working harder | Consider closing apps |
| **CRITICAL** | Red | Struggling, may swap heavily | Take action |

## Diagnostic Commands

### Check Memory Pressure

```bash
memory_pressure
```

This is the most important single command. It tells you if the system is actually struggling.

### Detailed Memory Statistics

```bash
vm_stat
```

Convert to human-readable:

```bash
vm_stat | perl -ne '/page size of (\d+)/ and $size=$1;
  /Pages (free|active|inactive|speculative|wired down|occupied by compressor|purgeable):\s+(\d+)/
  and printf "%-25s: %6.2f GB\n", $1, $2*$size/1073741824'
```

### Top Memory Consumers

```bash
# By memory percentage
ps aux -m | head -15

# With readable format
ps aux -m | head -15 | awk '{printf "%-6s %-8s %s\n", $4"%", $6/1024"MB", $11}'
```

### Swap Usage

```bash
sysctl vm.swapusage
```

High swap with memory pressure = insufficient RAM for workload.

## The `sudo purge` Command

### What It Does

Clears the disk cache (file system buffer cache) from RAM, making it available for applications.

### When to Use

- Memory pressure is WARN or CRITICAL
- System feels sluggish
- Before running memory-intensive tasks

### How to Use

```bash
sudo purge
```

### Important Notes

- **Safe to use** - no data loss
- **Temporary effect** - cache rebuilds over time
- **May cause brief slowdown** - as apps reload cached data
- **Not a permanent fix** - addresses symptom, not cause

## Common Memory Hogs

### Browsers

Web browsers are often the biggest memory consumers:

```bash
# Check browser memory
ps aux | grep -i "chrome\|safari\|firefox" | awk '{sum+=$6} END {print sum/1024 " MB"}'
```

**Tips:**
- Fewer tabs = less memory
- Use tab suspender extensions
- Close unused tabs regularly

### Electron Apps

Many modern apps use Electron (Slack, Discord, VS Code, Notion):

```bash
ps aux | grep -i "electron\|helper" | awk '{sum+=$6} END {print sum/1024 " MB"}'
```

**Tips:**
- Close when not in use
- Consider native alternatives
- Limit number of Electron apps running simultaneously

### Docker Desktop

Docker can consume significant RAM:

```bash
ps aux | grep -i docker | awk '{sum+=$6} END {print sum/1024 " MB"}'
```

**Tips:**
- Adjust memory limits in Docker Desktop preferences
- Stop when not actively developing
- Use Colima for lighter resource usage

## Apple Silicon vs Intel

### Apple Silicon (M1/M2/M3/M4)

- **Unified Memory Architecture (UMA)**: RAM shared between CPU and GPU
- **More efficient**: Generally handles memory better
- **Cannot upgrade**: Choose wisely at purchase
- **Recommended minimums**:
  - Light use: 8 GB
  - Development: 16 GB
  - Heavy workloads: 24-32 GB+

### Intel Macs

- **Separate RAM and VRAM**: Traditional architecture
- **Some models upgradeable**: Check your specific model
- **May benefit more from memory optimization**

## Optimization Strategies

### Immediate Relief

1. Close unused applications
2. Close browser tabs
3. Run `sudo purge`
4. Restart if nothing else helps

### Short-term

1. Identify and quit memory hogs
2. Reduce browser extensions
3. Limit Electron apps
4. Adjust Docker memory limits

### Long-term

1. Consider more RAM (new Mac purchase)
2. Use lighter alternatives (native vs Electron)
3. Develop habits to close unused apps
4. Regular restarts (weekly)

## When to Worry

### Don't Worry If

- High memory usage but pressure is OK
- Inactive memory is high (normal and good)
- Swap is used slightly (normal)

### Do Worry If

- Memory pressure is CRITICAL frequently
- Apps getting killed unexpectedly
- System very slow/unresponsive
- Swap usage is very high (>50% of RAM)
- "Your system has run out of application memory" messages

## Memory Myths

### Myth: "Free RAM is Good RAM"

**Reality**: macOS uses free RAM for caching. Unused RAM is wasted RAM.

### Myth: "Activity Monitor Memory Usage = Problem"

**Reality**: The Memory Pressure graph is what matters, not the usage bar.

### Myth: "Memory Cleaner Apps Help"

**Reality**: They usually just run `purge`, which you can do yourself. Many are unnecessary.

### Myth: "More RAM = Faster Mac"

**Reality**: Only if you're actually running out. More RAM helps with multitasking, not single-app speed.

## Reference Files

- **`references/memory-commands.md`** - All memory-related commands
