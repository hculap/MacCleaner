# How to Free Up Disk Space Quickly

When you're running low on disk space and need to reclaim gigabytes fast, follow this guide for the quickest wins.

## Quick Check: How Bad Is It?

Run a quick audit first:

```
/mac-cleaner:disk-audit
```

Look at the **Cleanup Opportunities** section to see where the biggest gains are.

## The Fast Track: Cache Cleanup

Caches are always safe to delete and often recover several gigabytes:

```
/mac-cleaner:disk-clean --type cache
```

**Typical recovery: 5-20 GB**

This cleans:
- User caches (`~/Library/Caches/`)
- System caches (`/Library/Caches/`)
- Browser caches (Chrome, Safari, Firefox)
- DNS cache

## Check Your Trash

Many people forget about Trash. Empty it:

```bash
# Check Trash size first
du -sh ~/.Trash
```

If it's large, empty it through Finder or:
```bash
rm -rf ~/.Trash/*
```

**Typical recovery: 0-50+ GB** (depends on usage)

## Developer? Clean Build Artifacts

If you're a developer, build artifacts are usually the biggest space hogs:

```
/mac-cleaner:disk-clean --type builds
```

This targets:
| Location | Typical Size |
|----------|--------------|
| Xcode DerivedData | 10-50 GB |
| iOS Simulators | 5-20 GB |
| node_modules | 5-30 GB |
| Rust target/ | 2-10 GB |
| Python venvs | 1-5 GB |

**Typical recovery: 20-100 GB**

## Using Docker? Prune It

Docker can silently consume huge amounts of space:

```
/mac-cleaner:disk-clean --type docker
```

Choose "Remove all unused resources" for best results.

**Typical recovery: 10-100 GB**

## Homebrew Cache

If you use Homebrew:

```
/mac-cleaner:disk-clean --type homebrew
```

**Typical recovery: 1-10 GB**

## Quick Wins Summary

| Action | Time | Typical Recovery |
|--------|------|------------------|
| Clean caches | 1 min | 5-20 GB |
| Empty Trash | 30 sec | 0-50 GB |
| Clean Xcode DerivedData | 1 min | 10-50 GB |
| Prune Docker | 2 min | 10-100 GB |
| Remove old node_modules | 5 min | 5-30 GB |
| Clean Homebrew | 1 min | 1-10 GB |

## The Nuclear Option

If you need maximum space immediately:

```
/mac-cleaner:disk-clean --type all
```

This runs through all cleanup categories interactively. Confirm only what you're comfortable deleting.

## After Cleanup

Run another audit to verify:

```
/mac-cleaner:disk-audit
```

Compare before/after to see how much you recovered.

## Still Need More Space?

If quick cleanup isn't enough:

1. **Check for large files**: Look at the "Large Files" section of your audit
2. **Review Downloads**: Old installers and DMGs add up
3. **Check Movies/Music**: Media files are often the biggest
4. **Consider cloud storage**: Move large files to iCloud, Dropbox, etc.
5. **Uninstall unused apps**: Check `/Applications` for apps you don't use
