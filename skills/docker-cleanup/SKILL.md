---
name: Docker Cleanup
description: Use when user asks about "Docker cleanup", "Docker disk space", "prune Docker", "Docker images", "Docker cache", "Docker volumes", "remove containers", or needs guidance on managing Docker resources on macOS.
version: 0.1.0
allowed-tools: Bash
---

# Docker Cleanup for macOS

Managing Docker Desktop disk usage and cleaning up Docker resources effectively.

## Docker on macOS

Docker Desktop for Mac runs a Linux VM to host containers. All Docker data is stored within this VM's disk image.

### Where Docker Stores Data

- **Disk Image**: `~/Library/Containers/com.docker.docker/Data/vms/0/data/Docker.raw`
- **Alternative**: `~/Library/Containers/com.docker.docker/Data/vms/0/data/Docker.qcow2`

This single file can grow very large (50GB+).

## Understanding Docker Disk Usage

### Check Current Usage

```bash
docker system df
```

This shows:
- **Images**: Downloaded and built images
- **Containers**: Stopped containers taking space
- **Volumes**: Persistent data volumes
- **Build Cache**: Cached layers from builds

### Detailed View

```bash
docker system df -v
```

## Cleanup Strategies

### Level 1: Safe Cleanup (Recommended First)

Removes only clearly unused resources:

```bash
# Remove stopped containers
docker container prune -f

# Remove dangling images (untagged)
docker image prune -f

# Remove unused networks
docker network prune -f
```

### Level 2: Standard Cleanup

Removes more, but preserves named volumes:

```bash
# Remove all unused containers, networks, and dangling images
docker system prune -f
```

### Level 3: Aggressive Cleanup

Removes everything not actively in use:

```bash
# Remove all unused images (not just dangling)
docker system prune -a -f
```

**Warning**: This removes all images not used by a running container.

### Level 4: Complete Cleanup (Including Volumes)

```bash
# Remove EVERYTHING including volumes
docker system prune -a --volumes -f
```

**Warning**: This deletes volume data permanently!

### Level 5: Build Cache

```bash
# Clear build cache
docker builder prune -f

# Clear all build cache
docker builder prune -a -f
```

## Cleanup Commands Reference

| Command | What It Removes | Data Loss Risk |
|---------|-----------------|----------------|
| `docker container prune` | Stopped containers | Low |
| `docker image prune` | Dangling images | None |
| `docker image prune -a` | All unused images | Medium (re-download) |
| `docker volume prune` | Unused volumes | **HIGH** |
| `docker network prune` | Unused networks | Low |
| `docker system prune` | Containers + images + networks | Low-Medium |
| `docker system prune -a` | Above + all unused images | Medium |
| `docker system prune -a --volumes` | Everything unused | **HIGH** |
| `docker builder prune` | Build cache | None (rebuilds slower) |

## Best Practices

### Regular Maintenance

1. **Weekly**: `docker system prune` - Clean basic unused resources
2. **Monthly**: `docker image prune -a` - Remove old images
3. **As Needed**: `docker builder prune` - When builds are slow/failing

### Before Pruning Volumes

Always check what you're about to delete:

```bash
# List all volumes
docker volume ls

# List unused volumes
docker volume ls -f dangling=true

# Inspect a volume before deleting
docker volume inspect volume_name
```

### Preserve Important Volumes

```bash
# Use labels to identify important volumes
docker volume create --label keep=true my_important_data

# Then filter prune
docker volume prune -f --filter "label!=keep=true"
```

## Docker Desktop Disk Space

### The Disk Image Problem

Docker Desktop's disk image only grows, never shrinks automatically.

### Reclaim Disk Space (macOS)

After pruning containers/images:

1. Open Docker Desktop
2. Settings → Resources → Advanced
3. Click "Purge data" or adjust disk image size
4. Or: Restart Docker Desktop

### Manual Disk Reclaim

```bash
# Stop Docker Desktop first!

# Find the disk image
ls -lh ~/Library/Containers/com.docker.docker/Data/vms/0/data/

# The .raw or .qcow2 file shows actual usage
```

### Factory Reset (Nuclear Option)

If disk image is huge and cleanup doesn't help:

1. Export any important volumes/data
2. Docker Desktop → Troubleshoot → Reset to factory defaults
3. Re-pull needed images

## Docker Compose Cleanup

### Remove Compose Project Resources

```bash
# Remove containers, networks, but keep volumes
docker compose down

# Remove everything including volumes
docker compose down -v

# Remove everything including images
docker compose down --rmi all
```

## Automated Cleanup

### Cleanup Script

```bash
#!/bin/bash
echo "Docker Cleanup Starting..."

echo "Removing stopped containers..."
docker container prune -f

echo "Removing dangling images..."
docker image prune -f

echo "Removing unused networks..."
docker network prune -f

echo "Removing build cache..."
docker builder prune -f

echo "Current usage:"
docker system df
```

### Scheduled Cleanup

Add to crontab for weekly cleanup:

```bash
0 0 * * 0 docker system prune -f > /dev/null 2>&1
```

## Common Issues

### "No space left on device"

Docker's VM disk is full:

```bash
# Check Docker disk usage
docker system df

# Aggressive cleanup
docker system prune -a -f
docker builder prune -a -f
```

### Disk Image Keeps Growing

Even after pruning, the disk image file doesn't shrink:

1. Use Docker Desktop's "Purge data" feature
2. Or restart Docker Desktop
3. Or increase `disk.sizeMiB` limit

### Slow Image Pulls After Cleanup

Normal - images need re-downloading. Consider keeping frequently used base images.

## Alternatives to Docker Desktop

For lighter resource usage:

| Alternative | Memory Usage | Disk Usage |
|-------------|--------------|------------|
| Docker Desktop | 2-4 GB | 20-60+ GB |
| Colima | 1-2 GB | 10-30 GB |
| Podman | 1-2 GB | 10-30 GB |
| OrbStack | 1-2 GB | 10-30 GB |

## Reference Files

- **`references/docker-commands.md`** - Complete Docker cleanup commands
