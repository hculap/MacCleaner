---
description: Free up RAM by purging inactive memory and identifying processes to close
allowed-tools: Bash, AskUserQuestion
argument-hint: [--purge] [--kill <process>]
---

# Memory Clean Command

Free up RAM through memory purging and process management: $ARGUMENTS

## Step 1: Parse Arguments

Parse `$ARGUMENTS` to extract:
- `--purge`: Run `sudo purge` to free inactive memory immediately
- `--kill <process>`: Terminate specific process by name

## Step 2: Current Memory Status (Before)

```bash
echo "=== Current Memory Status ==="
memory_pressure 2>/dev/null || echo "Memory pressure check unavailable"

# Get current available memory
vm_stat | perl -ne '/page size of (\d+)/ and $size=$1; /Pages (free|inactive):\s+(\d+)/ and $sum+=$2*$size/1073741824; END {printf "Approximate Available: %.2f GB\n", $sum}'
```

## Step 3: Handle --purge Flag

If `--purge` is specified:

```bash
echo "Running memory purge..."
sudo purge
echo "Purge complete."
```

**Note**: Explain to user:
- This clears disk cache from RAM
- Safe but may cause temporary slowdown as apps reload cached data
- Most effective when memory pressure is high

## Step 4: Handle --kill Flag

If `--kill <process>` is specified:

```bash
# Find and kill process
pkill -f "<process_name>"
# Or for more control:
killall "<process_name>" 2>/dev/null
```

## Step 5: Interactive Mode (No Flags)

If no flags provided, run interactive cleanup:

#### 4a: Show Top Memory Consumers

```bash
echo "=== Top Memory Consumers ==="
ps aux -m | head -11 | tail -10 | awk '{printf "%-6s %-8s %-40s\n", $4"%", $6/1024"MB", $11}'
```

#### 4b: Identify Candidates for Closing

Look for:
- Browsers with many helper processes
- Electron apps not in active use
- Background apps consuming significant memory
- Docker if not needed

#### 4c: Ask User for Action

Use AskUserQuestion:
- Question: "What would you like to do to free memory?"
- Options:
  - "Purge inactive memory (sudo purge)"
  - "Show apps I could close to free memory"
  - "Kill a specific heavy process"
  - "Do both: purge and show app suggestions"

## Step 6: Process Suggestions

If user wants suggestions:

```bash
echo "=== Suggested Actions ==="

# Check for browsers with many processes
browser_count=$(ps aux | grep -i "chrome\|safari\|firefox" | grep -v grep | wc -l)
browser_mem=$(ps aux | grep -i "chrome\|safari\|firefox" | grep -v grep | awk '{sum+=$6} END {print sum/1024}')
echo "Browsers: $browser_count processes using ${browser_mem}MB"

# Check for Electron apps
electron_mem=$(ps aux | grep -i "electron\|slack\|discord\|teams\|code helper" | grep -v grep | awk '{sum+=$6} END {print sum/1024}')
echo "Electron Apps: ${electron_mem}MB"

# Check Docker
docker_mem=$(ps aux | grep -i "docker" | grep -v grep | awk '{sum+=$6} END {print sum/1024}')
echo "Docker: ${docker_mem}MB"
```

Present actionable suggestions:
- "Close unused browser tabs (X browser processes using Y MB)"
- "Quit Slack/Discord if not needed (using Y MB)"
- "Stop Docker Desktop if not in use (using Y MB)"

## Step 7: Execute User Choice

Based on user selection, perform the appropriate action.

#### Purge Memory
```bash
sudo purge
```

#### Kill Specific App
```bash
# Kill by app name
osascript -e 'quit app "AppName"' 2>/dev/null || killall "AppName" 2>/dev/null
```

## Step 8: Memory Status (After)

```bash
echo "=== Memory Status After Cleanup ==="
memory_pressure 2>/dev/null

# Calculate freed memory
vm_stat | perl -ne '/page size of (\d+)/ and $size=$1; /Pages (free|inactive):\s+(\d+)/ and $sum+=$2*$size/1073741824; END {printf "Approximate Available: %.2f GB\n", $sum}'
```

## Report

```markdown
## Memory Cleanup Complete

### Before
- Memory Pressure: [status]
- Available Memory: ~X.XX GB

### Actions Taken
- [List of actions performed]

### After
- Memory Pressure: [status]
- Available Memory: ~X.XX GB
- **Freed**: ~X.XX GB

### Notes
- [Any relevant observations]
```

## Memory Management Tips

Provide context when relevant:

### About `sudo purge`
- Clears the disk cache (not app memory)
- Safe to use, no data loss
- May cause brief slowdown as cache rebuilds
- Best used when memory pressure is high

### When to Close Apps vs Purge
- **Close apps**: When specific apps are using too much memory
- **Purge**: When system is sluggish but apps aren't the problem
- **Restart**: When all else fails, a restart clears everything

### Preventing Memory Issues
- Use fewer browser tabs (or use tab suspender extension)
- Close apps when not in use
- Consider Docker Desktop resource limits
- Increase swap file size if regularly running low

## Safety Notes

1. **Never force-kill system processes**
2. **Warn before killing apps with unsaved data**
3. **Explain that purge is temporary** - cache will rebuild
4. **Don't over-optimize** - macOS manages memory well automatically
