# How to Clean node_modules

JavaScript projects accumulate massive `node_modules` folders. Old projects can waste gigabytes of disk space. This guide shows you how to find and clean them safely.

## The Problem

Every JavaScript/TypeScript project has a `node_modules` folder containing:
- Project dependencies
- Dependencies of dependencies
- Development tools

A single project can have 100MB to 1GB+ in node_modules.

If you have 20 old projects, that's potentially 20 GB of unused space.

## Find All node_modules

Run an audit to see the impact:

```
/mac-cleaner:disk-audit
```

Look for the node_modules section:

```
### node_modules Directories
| Size    | Location                          |
|---------|-----------------------------------|
| 1.2 GB  | ~/Projects/old-client-app         |
| 890 MB  | ~/Projects/archived-api           |
| 654 MB  | ~/Projects/test-project           |
| 432 MB  | ~/Projects/learning/react-tutorial|
```

Or find them manually:

```bash
find ~ -name "node_modules" -type d -prune 2>/dev/null | \
  while read d; do du -sh "$d"; done | sort -hr | head -20
```

## Quick Cleanup

Use MacCleaner's build artifacts cleanup:

```
/mac-cleaner:disk-clean --type builds
```

Select the node_modules directories you want to remove.

## Safe Cleanup Strategy

### Before Deleting, Check:

1. **Is the project active?**
   - Don't delete node_modules from projects you're actively working on

2. **Can you regenerate?**
   - You need `package.json` and `package-lock.json` (or `yarn.lock`)
   - Run `npm install` to restore

3. **Any local modifications?**
   - Rarely, projects have modified packages directly
   - If you edited files in node_modules, those changes will be lost

### Categories

| Project Type | Safe to Delete? | Notes |
|--------------|-----------------|-------|
| Archived/old projects | Yes | Can restore with npm install |
| Finished tutorials | Yes | Usually don't need these |
| Active projects | No | Would need to reinstall |
| Projects with custom patches | Caution | Check for local modifications |

## Manual Cleanup

### Delete from Specific Project

```bash
rm -rf ~/Projects/old-project/node_modules
```

### Delete from All Projects (Aggressive)

Find and delete all node_modules:

```bash
# Preview what will be deleted
find ~ -name "node_modules" -type d -prune 2>/dev/null

# Delete all (use with caution!)
find ~ -name "node_modules" -type d -prune -exec rm -rf {} \; 2>/dev/null
```

### Delete from Inactive Projects Only

A safer approach—only delete from projects not modified recently:

```bash
# Find node_modules in projects not modified in 30 days
find ~ -name "node_modules" -type d -prune 2>/dev/null | while read nm; do
  project_dir=$(dirname "$nm")
  last_mod=$(find "$project_dir" -maxdepth 1 -type f -name "*.js" -o -name "*.ts" -mtime -30 2>/dev/null | head -1)
  if [ -z "$last_mod" ]; then
    echo "Inactive: $nm"
  fi
done
```

## Restoring node_modules

If you need to work on a project again:

```bash
cd ~/Projects/my-project
npm install
```

Or with yarn:
```bash
yarn install
```

## Reducing node_modules Size

### Use npm ci Instead of npm install

`npm ci` creates smaller, more consistent installs:

```bash
npm ci
```

### Prune Development Dependencies in Production

```bash
npm prune --production
```

### Use pnpm

pnpm shares dependencies across projects:

```bash
# Install pnpm
npm install -g pnpm

# Use for projects
pnpm install
```

This can save 50%+ disk space across multiple projects.

## Preventing Bloat

### 1. Delete Old Projects Completely

If you're done with a project, delete it entirely:
```bash
rm -rf ~/Projects/old-tutorial
```

### 2. Use .gitignore Properly

Never commit node_modules to git (but you probably knew that).

### 3. Regular Cleanup

Add to your monthly maintenance:
```
/mac-cleaner:disk-clean --type builds
```

### 4. Archive Instead of Keep

If you might need a project later, archive it:
```bash
# Remove node_modules before archiving
rm -rf my-project/node_modules
zip -r my-project.zip my-project
rm -rf my-project
```

## Other Package Manager Caches

JavaScript package managers have their own caches too:

### npm Cache

```bash
# Check size
du -sh ~/.npm

# Clean cache
npm cache clean --force
```

### Yarn Cache

```bash
# Check size
du -sh ~/.yarn/cache

# Clean cache
yarn cache clean
```

### pnpm Cache

```bash
# Check size
du -sh ~/.pnpm-store

# Clean unused packages
pnpm store prune
```

## Complete JavaScript Cleanup

For maximum space recovery:

```bash
# 1. Clean all package manager caches
npm cache clean --force
yarn cache clean 2>/dev/null
pnpm store prune 2>/dev/null

# 2. Delete all node_modules (careful!)
find ~ -name "node_modules" -type d -prune -exec rm -rf {} \; 2>/dev/null

# 3. Clean npm global temp
rm -rf ~/.npm/_cacache/*
```

Then reinstall only the projects you actively use.

## Checking Results

After cleanup:

```
/mac-cleaner:disk-audit
```

Compare the node_modules section to see how much you saved.
