# How to Clean Xcode Build Files

Xcode can consume 50-100+ GB with build artifacts, simulators, and archives. This guide shows you how to reclaim that space safely.

## Check Current Usage

See how much Xcode is using:

```
/mac-cleaner:disk-audit
```

Look for developer directories:

```
### Developer Directories
| Location                                    | Size    |
|---------------------------------------------|---------|
| ~/Library/Developer                         | 78 GB   |
| ~/Library/Developer/Xcode/DerivedData       | 32 GB   |
| ~/Library/Developer/Xcode/Archives          | 15 GB   |
| ~/Library/Developer/CoreSimulator           | 25 GB   |
```

## Quick Cleanup

Run the build artifacts cleanup:

```
/mac-cleaner:disk-clean --type builds
```

This targets all Xcode-related caches.

## What You Can Clean

### DerivedData (Always Safe)

**Location:** `~/Library/Developer/Xcode/DerivedData/`
**Typical size:** 10-50 GB

Contains:
- Compiled code
- Index files
- Build logs

**Safe to delete:** Yes, always. Xcode rebuilds when you compile.

```bash
# Manual cleanup
rm -rf ~/Library/Developer/Xcode/DerivedData/*
```

Or in Xcode: **Xcode → Settings → Locations → Derived Data** → click arrow → delete folder contents.

### Archives (Check First)

**Location:** `~/Library/Developer/Xcode/Archives/`
**Typical size:** 5-30 GB

Contains:
- App builds for distribution
- dSYM files for crash reporting
- TestFlight/App Store submissions

**Safe to delete:**
- Old archives you've already submitted
- Archives for apps no longer maintained
- Duplicate archives

**Keep:**
- Recent submissions (for crash symbolication)
- Archives you might need to re-submit

```bash
# View archives
ls -la ~/Library/Developer/Xcode/Archives/
```

Or use Xcode Organizer: **Window → Organizer → Archives**

### iOS Simulators (Selective)

**Location:** `~/Library/Developer/CoreSimulator/`
**Typical size:** 10-40 GB

Each simulator can be 1-2 GB.

**Safe to delete:**
- Simulators for old iOS versions you don't target
- Duplicate device types
- Unavailable/broken simulators

```bash
# Delete unavailable simulators
xcrun simctl delete unavailable

# List all simulators
xcrun simctl list devices
```

**Keep:**
- Simulators for iOS versions you actively test
- Device types you use regularly

### Xcode Caches

**Location:** `~/Library/Caches/com.apple.dt.Xcode/`
**Typical size:** 1-5 GB

**Safe to delete:** Yes, Xcode rebuilds these.

```bash
rm -rf ~/Library/Caches/com.apple.dt.Xcode/*
```

### Device Support Files

**Location:** `~/Library/Developer/Xcode/iOS DeviceSupport/`
**Typical size:** 5-20 GB

Contains support files for each iOS version you've connected via device.

**Safe to delete:** Old versions. Will re-download if you connect a device with that version.

```bash
# View device support
ls -la ~/Library/Developer/Xcode/iOS\ DeviceSupport/
```

## Complete Xcode Cleanup Checklist

| Item | Location | Size | Safety |
|------|----------|------|--------|
| DerivedData | `~/Library/Developer/Xcode/DerivedData/` | 10-50 GB | Always safe |
| Caches | `~/Library/Caches/com.apple.dt.Xcode/` | 1-5 GB | Always safe |
| Unavailable simulators | CoreSimulator | Varies | Safe |
| Old device support | iOS DeviceSupport | 5-20 GB | Safe |
| Old archives | Xcode/Archives | 5-30 GB | Check first |
| Old simulators | CoreSimulator | 1-2 GB each | Check first |

## Automating Cleanup

### Using MacCleaner

Add to your monthly routine:

```
/mac-cleaner:disk-clean --type builds
```

### Using Terminal

Create a cleanup script:

```bash
#!/bin/bash
echo "Cleaning Xcode..."

# DerivedData
rm -rf ~/Library/Developer/Xcode/DerivedData/*
echo "✓ DerivedData cleared"

# Caches
rm -rf ~/Library/Caches/com.apple.dt.Xcode/*
echo "✓ Caches cleared"

# Unavailable simulators
xcrun simctl delete unavailable
echo "✓ Unavailable simulators removed"

echo "Done!"
```

## Preventing Bloat

### 1. Limit Simulators

Only download simulators you actually use:

**Xcode → Settings → Platforms** → Delete old runtime versions

### 2. Regular DerivedData Cleanup

DerivedData grows indefinitely. Clean it monthly or after finishing projects.

### 3. Archive Wisely

Don't archive every build—only builds you might submit or need later.

### 4. Use Build System Settings

In project settings:
- Enable "Build Active Architecture Only" for Debug
- Use "Optimize Build" settings

## Recovering Disk Space Without Losing Data

If you need space but want to keep the ability to rebuild:

1. **DerivedData**: Delete it—rebuilds automatically
2. **Simulators**: Delete old iOS versions—re-download if needed
3. **Device Support**: Delete old versions—re-syncs when device connects
4. **Archives**: Export IPA first, then delete archive

## Checking Results

After cleanup:

```
/mac-cleaner:disk-audit
```

Compare the Developer directories section before and after.

## Common Issues

### "Cannot delete DerivedData"

Close Xcode first, then delete.

### "Simulator won't launch after cleanup"

Reset the simulator:
```bash
xcrun simctl erase <device_id>
```

Or reinstall the runtime:
**Xcode → Settings → Platforms → [+]**

### "Build fails after cleanup"

Normal—just rebuild. The first build after cleaning DerivedData takes longer.

If issues persist:
```bash
# Clean build folder
xcodebuild clean

# Delete module cache
rm -rf ~/Library/Developer/Xcode/DerivedData/ModuleCache.noindex/
```
