# macOS Cache Locations Reference

## User-Level Caches

### General Cache Directory
- **Path**: `~/Library/Caches/`
- **Safety**: Safe to delete contents
- **Regeneration**: Apps recreate on next launch

### Browser Caches

| Browser | Cache Location |
|---------|----------------|
| Safari | `~/Library/Caches/com.apple.Safari/` |
| Chrome | `~/Library/Caches/Google/Chrome/` |
| Firefox | `~/Library/Caches/Firefox/` |
| Edge | `~/Library/Caches/Microsoft Edge/` |
| Brave | `~/Library/Caches/BraveSoftware/` |

### Application Caches (Common Large Ones)

| Application | Cache Location | Typical Size |
|-------------|----------------|--------------|
| Spotify | `~/Library/Caches/com.spotify.client/` | 1-5 GB |
| VS Code | `~/Library/Caches/com.microsoft.VSCode/` | 500 MB - 2 GB |
| Slack | `~/Library/Caches/com.tinyspeck.slackmacgap/` | 200 MB - 1 GB |
| Discord | `~/Library/Caches/com.hnc.Discord/` | 200 MB - 1 GB |
| Xcode | `~/Library/Caches/com.apple.dt.Xcode/` | 1-10 GB |

## System-Level Caches

### System Cache Directory
- **Path**: `/Library/Caches/`
- **Safety**: Safe to delete contents (requires sudo)
- **Note**: Keep folder structure, delete only contents

### Kernel Extension Cache
- **Path**: `/System/Library/Caches/`
- **Safety**: DO NOT DELETE - System managed

## Developer Caches

### Xcode
| Purpose | Location | Size Range |
|---------|----------|------------|
| DerivedData | `~/Library/Developer/Xcode/DerivedData/` | 5-100 GB |
| Archives | `~/Library/Developer/Xcode/Archives/` | 1-50 GB |
| iOS DeviceSupport | `~/Library/Developer/Xcode/iOS DeviceSupport/` | 5-30 GB |
| watchOS DeviceSupport | `~/Library/Developer/Xcode/watchOS DeviceSupport/` | 1-5 GB |

### iOS Simulators
- **Path**: `~/Library/Developer/CoreSimulator/Devices/`
- **Size**: 10-50 GB
- **Cleanup**: `xcrun simctl delete unavailable`

### Package Managers

| Manager | Cache Location |
|---------|----------------|
| npm | `~/.npm/` |
| yarn | `~/.yarn/cache/` or `~/Library/Caches/Yarn/` |
| pnpm | `~/.pnpm-store/` |
| pip | `~/Library/Caches/pip/` |
| Homebrew | `$(brew --cache)` (usually `~/Library/Caches/Homebrew/`) |
| Cargo | `~/.cargo/registry/cache/` |
| CocoaPods | `~/Library/Caches/CocoaPods/` |
| Gradle | `~/.gradle/caches/` |
| Maven | `~/.m2/repository/` |

## Log Locations

| Type | Location | Safety |
|------|----------|--------|
| User Logs | `~/Library/Logs/` | Safe to delete |
| System Logs | `/private/var/log/` | Safe (sudo required) |
| Crash Reports | `~/Library/Logs/DiagnosticReports/` | Safe to delete |

## Temporary Files

| Type | Location | Notes |
|------|----------|-------|
| User Temp | `$TMPDIR` or `/var/folders/` | Auto-cleaned |
| System Temp | `/tmp/`, `/private/tmp/` | Auto-cleaned on reboot |

## Quick Size Check Commands

```bash
# Check all caches at once
echo "User Caches:"; du -sh ~/Library/Caches 2>/dev/null
echo "System Caches:"; du -sh /Library/Caches 2>/dev/null
echo "Logs:"; du -sh ~/Library/Logs 2>/dev/null
echo "Xcode DerivedData:"; du -sh ~/Library/Developer/Xcode/DerivedData 2>/dev/null
echo "iOS Simulators:"; du -sh ~/Library/Developer/CoreSimulator 2>/dev/null
echo "npm:"; du -sh ~/.npm 2>/dev/null
echo "Homebrew:"; du -sh $(brew --cache 2>/dev/null) 2>/dev/null
```
