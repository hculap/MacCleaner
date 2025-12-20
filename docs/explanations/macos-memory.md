# How macOS Manages Memory

Understanding how macOS handles RAM helps you interpret memory reports and know when action is actually needed.

## The Core Concept: Use All Available RAM

Unlike some operating systems, macOS follows a philosophy:

**Unused RAM is wasted RAM.**

Instead of leaving memory empty "just in case," macOS actively uses available RAM to make your Mac faster:
- Caching frequently accessed files
- Keeping recently used app data ready
- Pre-loading commonly used system components

This means **seeing low "free" memory is completely normal** and not a problem.

## What Matters: Memory Pressure

The single most important metric is **Memory Pressure**, not raw memory usage.

Memory Pressure indicates how hard your system is working to manage memory:

| Status | Color | Meaning |
|--------|-------|---------|
| OK | Green | System is healthy, plenty of headroom |
| WARN | Yellow | System is coping but working harder |
| CRITICAL | Red | System is struggling, take action |

**How to check:**
- Activity Monitor → Memory tab → Memory Pressure graph
- `/mac-cleaner:memory-audit`

## Memory Types Explained

### Wired Memory

Memory that **must** stay in physical RAM.
- Kernel and core system processes
- Active device drivers
- Security-critical data

**Cannot be freed.** This is essential system overhead.

Typical wired memory: 2-6 GB depending on what's running.

### Active Memory

Memory currently being used by running applications.
- Application code
- Working data
- Open documents

**Freed when apps close.** The more apps you run, the more active memory used.

### Inactive Memory

Memory recently used but no longer actively needed.
- Recently closed apps
- Recently accessed files
- Cached data

**Available instantly.** When an app needs memory, inactive memory is reclaimed immediately with no delay.

This is why "low free memory" isn't a problem—inactive memory IS available memory.

### Compressed Memory

Active memory that macOS has compressed to save space.
- Data that isn't being accessed often
- Compressed using efficient algorithms
- Expands transparently when needed

Apple Silicon Macs are especially good at this.

### Free Memory

Truly unused memory.
- Nothing is stored here
- Immediately available

Often very low—macOS prefers to use RAM for caching rather than leaving it empty.

## The Memory Lifecycle

1. **App launches** → Uses free or inactive memory
2. **App runs** → Memory becomes Active
3. **App idles** → Memory may be Compressed
4. **App closes** → Memory becomes Inactive
5. **Time passes** → Inactive memory may be reused
6. **New app launches** → Inactive memory becomes Active

This cycle happens constantly and automatically.

## When macOS Needs More Memory

If all memory is in use and an app needs more:

1. **Reclaim inactive memory** (instant)
2. **Decompress compressed memory** (fast)
3. **Compress active memory** (fast)
4. **Swap to disk** (slow)

Steps 1-3 happen seamlessly without you noticing.

Step 4 (swapping) is where performance suffers.

## Swap: The Warning Sign

**Swap** is disk space used as emergency RAM.

When swap is heavily used:
- Disk reads/writes increase dramatically
- System becomes slow and unresponsive
- Apps may beach-ball frequently

**Swap usage guidelines:**

| Swap Used | Interpretation |
|-----------|----------------|
| < 1 GB | Normal, nothing to worry about |
| 1-4 GB | Elevated but acceptable |
| 4-8 GB | Your workload is memory-heavy |
| > 8 GB | Consider closing apps or getting more RAM |

Some swap usage is normal. Heavy swap usage indicates your RAM is insufficient for your workload.

## The "Purge" Command

`sudo purge` clears the **disk cache** from RAM.

**What it does:**
- Frees memory used for caching files
- Makes that memory available to apps
- Does NOT close apps or delete data

**What happens after:**
- Apps feel slightly slower initially
- Disk access increases temporarily
- Caches rebuild as you use your Mac

**When to use:**
- Memory pressure is high
- You're about to run a memory-intensive task
- As a temporary fix

**When NOT to use:**
- Memory pressure is OK
- As regular maintenance (caches help performance)
- Expecting permanent improvement (caches return)

## Apple Silicon vs Intel

### Apple Silicon (M1/M2/M3)

- **Unified Memory**: RAM shared between CPU and GPU
- **Efficient compression**: Better memory efficiency
- **Higher bandwidth**: Faster memory access
- **Cannot upgrade**: What you buy is what you get

8 GB on Apple Silicon performs roughly like 12-16 GB on Intel for many tasks.

### Intel Macs

- **Separate GPU memory** (discrete) or shared (integrated)
- **Upgradeable** (on many models)
- **Higher base requirement**: Often needs more RAM for same workload

## How Much RAM Do You Need?

| Usage | Recommended |
|-------|-------------|
| Web, email, documents | 8 GB |
| Development (light) | 8-16 GB |
| Development (Docker, VMs) | 16-32 GB |
| Photo/video editing | 16-32 GB |
| Heavy development + Docker + Simulators | 32+ GB |

When in doubt, check memory pressure during your typical workload.

## Myths About Mac Memory

### Myth: "I need to keep free memory high"

**Reality:** macOS works best when it uses all RAM. Free memory sitting unused doesn't help anything.

### Myth: "Closing apps frees memory immediately"

**Reality:** Memory becomes inactive, not free. This is by design—if you reopen the app, it loads faster.

### Myth: "Memory cleaners help performance"

**Reality:** Most just run `purge`, which provides temporary relief at the cost of slower disk access afterward.

### Myth: "High memory usage means I need more RAM"

**Reality:** Check memory pressure, not usage. High usage with green pressure means macOS is efficiently using your RAM.

## When to Actually Worry

**Take action if you see:**
- Memory pressure consistently CRITICAL (red)
- Swap usage over 4-8 GB regularly
- Apps being force-quit by the system
- "Your system has run out of application memory" dialogs
- Persistent slowness even with few apps open

**Don't worry about:**
- Low "free" memory
- High memory "used" percentage
- Brief memory pressure spikes during heavy tasks
- Swap usage under 1-2 GB

## Practical Tips

1. **Check pressure, not usage**
2. **Close apps you're not using** (unlike files, they use RAM)
3. **Use Safari over Chrome** (generally more memory-efficient)
4. **Limit browser tabs** (each tab uses memory)
5. **Stop Docker when not needed** (uses 2-6 GB minimum)
6. **Restart weekly** (clears accumulated memory leaks)

## Summary

| Metric | Normal | Concerning |
|--------|--------|------------|
| Free memory | Low (near zero) | N/A |
| Memory pressure | Green | Red consistently |
| Swap | < 2 GB | > 8 GB |
| Compressed | Any amount | N/A |

Trust macOS to manage memory. Focus on memory pressure and swap, not raw usage numbers.
