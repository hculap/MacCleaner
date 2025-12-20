# Developer Cleanup Commands Reference

## Node.js / JavaScript

### Find node_modules
```bash
find ~ -name "node_modules" -type d -prune 2>/dev/null
```

### Size of node_modules
```bash
find ~ -name "node_modules" -type d -prune 2>/dev/null | xargs du -sh 2>/dev/null | sort -hr
```

### npm Cache
```bash
# Size
du -sh ~/.npm

# Clean
npm cache clean --force

# Verify
npm cache verify
```

### Yarn Cache
```bash
# Size
yarn cache dir
du -sh $(yarn cache dir)

# Clean
yarn cache clean
```

### pnpm Store
```bash
# Size
du -sh ~/.pnpm-store

# Prune
pnpm store prune
```

## Xcode / iOS

### DerivedData
```bash
# Size
du -sh ~/Library/Developer/Xcode/DerivedData

# Clean all
rm -rf ~/Library/Developer/Xcode/DerivedData/*
```

### Archives
```bash
# Size
du -sh ~/Library/Developer/Xcode/Archives

# Open in Finder
open ~/Library/Developer/Xcode/Archives
```

### iOS Simulators
```bash
# List
xcrun simctl list devices

# Delete unavailable
xcrun simctl delete unavailable

# Delete specific
xcrun simctl delete <UUID>

# Erase all data
xcrun simctl erase all

# Shutdown all
xcrun simctl shutdown all
```

### Device Support
```bash
# iOS
du -sh ~/Library/Developer/Xcode/iOS\ DeviceSupport
rm -rf ~/Library/Developer/Xcode/iOS\ DeviceSupport/14.*  # old versions

# watchOS
du -sh ~/Library/Developer/Xcode/watchOS\ DeviceSupport

# tvOS
du -sh ~/Library/Developer/Xcode/tvOS\ DeviceSupport
```

### CocoaPods
```bash
# Cache size
du -sh ~/Library/Caches/CocoaPods

# Clean
pod cache clean --all
```

### Carthage
```bash
# Cache
du -sh ~/Library/Caches/org.carthage.CarthageKit

# Clean
rm -rf ~/Library/Caches/org.carthage.CarthageKit
```

## Rust

### Cargo Clean (per project)
```bash
cargo clean
```

### Registry Cache
```bash
du -sh ~/.cargo/registry/cache
rm -rf ~/.cargo/registry/cache
```

### Find target/ directories
```bash
find ~ -name "target" -type d -path "*/target" 2>/dev/null | xargs du -sh 2>/dev/null | sort -hr
```

## Python

### pip Cache
```bash
# Size
du -sh ~/Library/Caches/pip

# Clean
pip cache purge
```

### Find venvs
```bash
find ~ -type d \( -name "venv" -o -name ".venv" \) 2>/dev/null | xargs du -sh 2>/dev/null | sort -hr
```

### Clean __pycache__
```bash
find ~ -name "__pycache__" -type d -exec rm -rf {} \; 2>/dev/null
```

### Clean .pyc files
```bash
find ~ -name "*.pyc" -delete 2>/dev/null
```

### Poetry Cache
```bash
du -sh ~/Library/Caches/pypoetry
poetry cache clear --all pypi
```

## Java / Kotlin

### Gradle
```bash
# Cache size
du -sh ~/.gradle/caches

# Clean caches
rm -rf ~/.gradle/caches

# Project clean
./gradlew clean
```

### Maven
```bash
# Size
du -sh ~/.m2/repository

# Project clean
mvn clean

# Full clean
rm -rf ~/.m2/repository
```

## Go

### Module Cache
```bash
# Size
du -sh ~/go/pkg/mod

# Clean
go clean -modcache
```

### Build Cache
```bash
# Clean
go clean -cache

# Both
go clean -cache -modcache
```

## Flutter / Dart

### Pub Cache
```bash
du -sh ~/.pub-cache

# Clean
flutter pub cache clean
```

### Flutter/Dart SDKs
```bash
du -sh ~/flutter
```

## Ruby

### Bundler Cache
```bash
du -sh ~/.bundle
bundle clean --force
```

### Gem Cache
```bash
gem cleanup
```

## IDE Caches

### VS Code
```bash
du -sh ~/Library/Application\ Support/Code/
```

### JetBrains
```bash
# All JetBrains IDEs
du -sh ~/Library/Caches/JetBrains/
du -sh ~/Library/Application\ Support/JetBrains/
```

## Quick Size Check

```bash
echo "=== Dev Environment Sizes ==="
echo "Xcode DerivedData:"; du -sh ~/Library/Developer/Xcode/DerivedData 2>/dev/null
echo "Xcode Archives:"; du -sh ~/Library/Developer/Xcode/Archives 2>/dev/null
echo "iOS Simulators:"; du -sh ~/Library/Developer/CoreSimulator 2>/dev/null
echo "npm cache:"; du -sh ~/.npm 2>/dev/null
echo "yarn cache:"; du -sh $(yarn cache dir 2>/dev/null) 2>/dev/null
echo "Gradle:"; du -sh ~/.gradle/caches 2>/dev/null
echo "Maven:"; du -sh ~/.m2/repository 2>/dev/null
echo "Cargo:"; du -sh ~/.cargo 2>/dev/null
echo "Go modules:"; du -sh ~/go/pkg/mod 2>/dev/null
echo "pip cache:"; du -sh ~/Library/Caches/pip 2>/dev/null
```
