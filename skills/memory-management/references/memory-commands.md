# macOS Memory Commands Reference

## Diagnostic Commands

### Memory Pressure (Most Important)
```bash
memory_pressure
```
Returns: System-wide memory status (OK/WARN/CRITICAL)

### Virtual Memory Statistics
```bash
vm_stat
```
Shows page statistics. Multiply by page size (usually 16384) to get bytes.

### Human-Readable Memory Breakdown
```bash
vm_stat | perl -ne '/page size of (\d+)/ and $size=$1;
  /Pages (free|active|inactive|speculative|wired down|occupied by compressor|purgeable):\s+(\d+)/
  and printf "%-25s: %6.2f GB\n", $1, $2*$size/1073741824'
```

### Total System RAM
```bash
sysctl -n hw.memsize | awk '{print $1/1024/1024/1024 " GB"}'
```

### Swap Usage
```bash
sysctl vm.swapusage
```

## Process Commands

### Top Memory Consumers
```bash
# Top 15 by memory
ps aux -m | head -16

# Formatted nicely
ps aux -m | head -16 | awk '{printf "%-6s %-10s %s\n", $4"%", $6/1024"MB", $11}'
```

### Memory by Application Category
```bash
# Browsers
ps aux | grep -iE "chrome|safari|firefox|edge" | grep -v grep | awk '{sum+=$6} END {printf "Browsers: %.0f MB\n", sum/1024}'

# Electron apps
ps aux | grep -iE "electron|slack|discord|code.helper|notion" | grep -v grep | awk '{sum+=$6} END {printf "Electron: %.0f MB\n", sum/1024}'

# Docker
ps aux | grep -i docker | grep -v grep | awk '{sum+=$6} END {printf "Docker: %.0f MB\n", sum/1024}'
```

### Find Specific Process Memory
```bash
# Replace "ProcessName" with actual name
ps aux | grep -i "ProcessName" | grep -v grep | awk '{print $6/1024 " MB"}'
```

## Memory Management Commands

### Purge Inactive Memory
```bash
sudo purge
```

### Clear DNS Cache
```bash
sudo dscacheutil -flushcache
sudo killall -HUP mDNSResponder
```

## Application Control

### Quit Application Gracefully
```bash
osascript -e 'quit app "AppName"'
```

### Force Quit Application
```bash
killall "AppName"
# or
pkill -f "AppName"
```

### Quit All Applications (Careful!)
```bash
osascript -e 'tell application "System Events" to set the visible of every process to true'
osascript -e 'tell application "System Events" to keystroke "q" using command down'
```

## Monitoring Commands

### Continuous Memory Monitoring
```bash
# Update every 2 seconds
while true; do clear; memory_pressure; sleep 2; done
```

### Watch Top Memory Processes
```bash
top -o mem -n 10
```

### Activity Monitor (GUI)
```bash
open -a "Activity Monitor"
```

## System Information

### Hardware Memory Details
```bash
system_profiler SPMemoryDataType
```

### Check if Apple Silicon
```bash
uname -m
# arm64 = Apple Silicon
# x86_64 = Intel
```

## Swap File Location
```bash
ls -la /private/var/vm/swapfile*
```
