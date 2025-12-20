# Tutorial: Optimizing Mac Memory

This tutorial teaches you how to analyze memory usage, understand what the numbers mean, and free up RAM when needed.

## What You'll Learn

- How to check memory pressure
- How to interpret memory statistics
- When to take action (and when not to)
- How to free up memory effectively

## Prerequisites

- MacCleaner plugin installed
- 10 minutes

## Step 1: Check Memory Pressure

Start with a memory audit:

```
/mac-cleaner:memory-audit
```

The most important output is the **Memory Pressure** status:

```
### Memory Pressure
**Status**: OK
```

This is what matters most:
- **OK** (Green): Your Mac is fine, no action needed
- **WARN** (Yellow): System is coping but could use help
- **CRITICAL** (Red): Take action now

## Step 2: Understand the Memory Breakdown

You'll see something like:

```
### Memory Breakdown
| Type        | Size     | Description                    |
|-------------|----------|--------------------------------|
| Wired       | 4.23 GB  | Locked in RAM, cannot be freed |
| Active      | 8.12 GB  | Currently in use by apps       |
| Inactive    | 3.45 GB  | Recently used, can be freed    |
| Compressed  | 2.87 GB  | Compressed to save space       |
| Free        | 0.33 GB  | Immediately available          |
```

**Don't panic about low "Free" memory!** This is normal.

### Why "Free" Is Usually Low

macOS uses a simple philosophy: **unused RAM is wasted RAM**.

Instead of leaving memory empty, macOS:
- Caches frequently accessed data
- Keeps recently used app data ready
- Compresses inactive memory to fit more

When an app needs more memory, macOS instantly reclaims inactive memory.

### What Each Type Means

| Type | Can Be Freed? | Action |
|------|---------------|--------|
| Wired | No | System needs this |
| Active | When app closes | Close apps to free |
| Inactive | Yes, instantly | Available on demand |
| Compressed | When decompressed | System manages this |
| Free | N/A | Already available |

**Effective available memory** = Free + Inactive

## Step 3: Identify Memory Hogs

Check the top memory consumers:

```
### Top Memory Consumers
| Memory % | Size     | Process                 |
|----------|----------|-------------------------|
| 12.3%    | 1.97 GB  | Google Chrome Helper    |
| 8.7%     | 1.39 GB  | com.docker.hyperkit     |
| 5.2%     | 832 MB   | Slack Helper            |
| 4.1%     | 656 MB   | Code Helper (VS Code)   |
```

Common culprits:
- **Browsers** (especially Chrome with many tabs)
- **Docker** (runs a full Linux VM)
- **Electron apps** (Slack, Discord, VS Code)
- **IDEs** (Xcode, IntelliJ)

## Step 4: Check Swap Usage

Swap indicates if you're truly running low:

```
### Swap Status
- **Swap Used**: 1.2 GB of 4.0 GB
- **Interpretation**: Normal
```

| Swap Level | Meaning |
|------------|---------|
| < 1 GB | Normal |
| 1-4 GB | Elevated but okay |
| > 4 GB | Consider closing apps |
| > 8 GB | Memory insufficient for workload |

## Step 5: Decide If Action Is Needed

**NO ACTION NEEDED if:**
- Memory Pressure is OK or WARN
- Your Mac feels responsive
- Swap is under 4 GB

**TAKE ACTION if:**
- Memory Pressure is CRITICAL
- Mac is noticeably slow
- Apps are crashing
- You see "Your system has run out of application memory"

## Step 6: Free Up Memory

If you need to free memory, run:

```
/mac-cleaner:memory-clean
```

You'll be asked what to do:

```
What would you like to do to free memory?
○ Purge inactive memory (sudo purge)
○ Show apps I could close to free memory
○ Kill a specific heavy process
○ Do both: purge and show app suggestions
```

### Option: Purge Memory

Purge clears the disk cache from RAM:

```
/mac-cleaner:memory-clean --purge
```

**What happens:**
- Disk cache is cleared
- Memory becomes available
- Apps may feel slightly slower temporarily
- No data is lost

**When to use:**
- Memory pressure is high
- You're about to run a heavy app
- Quick temporary fix needed

### Option: Close Heavy Apps

For lasting improvement, close memory-hungry apps:

1. Review the suggestions
2. Close browsers tabs you don't need
3. Quit apps you're not using
4. Consider lighter alternatives

**Browser tips:**
- Use OneTab or similar to suspend tabs
- Close old tabs instead of keeping them
- Consider Safari over Chrome (uses less memory)

**Docker tip:**
- Stop Docker Desktop when not needed
- It uses 2-6 GB just running idle

## Step 7: Verify Improvement

After taking action, run another audit:

```
/mac-cleaner:memory-audit
```

Compare:
- Memory Pressure status
- Swap usage
- Available memory

## Memory Optimization Tips

### For Daily Use

1. **Close unused apps** - Don't leave apps open "just in case"
2. **Manage browser tabs** - Use bookmarks instead of open tabs
3. **Restart weekly** - Clears accumulated memory leaks
4. **Check Activity Monitor** - Know what's using memory

### For Developers

1. **Limit Docker resources** - Docker Desktop → Preferences → Resources
2. **Close simulators** - iOS/Android simulators use 500MB+ each
3. **Use lighter tools** - VS Code vs Xcode for simple edits
4. **Close unused IDEs** - Don't keep multiple IDEs open

### For Apple Silicon Macs

Apple Silicon (M1/M2/M3) handles memory differently:
- Unified memory shared between CPU and GPU
- More efficient memory usage overall
- Memory cannot be upgraded after purchase
- 8 GB works for most users; 16+ GB for heavy work

## When to Consider More RAM

If you frequently see:
- Memory Pressure at CRITICAL
- Swap usage over 8 GB
- Apps being killed by the system
- Regular slowdowns

...your workload may exceed your RAM capacity.

**For new Macs:** Consider 16 GB minimum for development work, 32 GB for video/data work.

**For existing Macs:** Intel Macs can sometimes upgrade RAM; Apple Silicon cannot.

## Next Steps

- **[How macOS manages memory](../explanations/macos-memory.md)** - Deeper technical explanation
- **[Troubleshooting guide](../how-to/troubleshooting.md)** - When things go wrong
- **[Free up disk space](./disk-cleanup.md)** - Disk affects memory via swap
