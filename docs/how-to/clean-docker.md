# How to Clean Docker on Mac

Docker can silently consume 50-100+ GB on your Mac. This guide shows you how to reclaim that space.

## Check Current Usage

First, see how much Docker is using:

```
/mac-cleaner:disk-audit
```

Look for the Docker section:

```
### Docker
- Container disk image: 45.2 GB
```

Or check directly:

```bash
docker system df
```

This shows:
```
TYPE            TOTAL     ACTIVE    SIZE      RECLAIMABLE
Images          45        12        12.5GB    8.2GB (65%)
Containers      23        3         1.2GB     1.1GB (92%)
Local Volumes   18        5         15.3GB    12.1GB (79%)
Build Cache     0         0         4.5GB     4.5GB
```

**Reclaimable** = space you can free.

## Quick Cleanup

Run the Docker cleanup:

```
/mac-cleaner:disk-clean --type docker
```

Choose your cleanup level:

| Option | What It Removes | Safety |
|--------|-----------------|--------|
| Unused images only | Images not used by containers | Safe |
| All unused resources | Images, stopped containers, networks | Safe |
| Everything including volumes | All unused + volumes with data | Caution |
| Build cache | Docker layer cache | Safe |

## Understanding What Gets Removed

### Images

Docker images are the blueprints for containers.

**Removed:** Images not referenced by any container (running or stopped)
**Kept:** Images used by existing containers

```bash
# View images
docker images

# See what would be pruned
docker image prune --dry-run
```

### Containers

Containers are running (or stopped) instances of images.

**Removed:** Stopped containers
**Kept:** Running containers

```bash
# View all containers
docker ps -a

# See stopped containers
docker ps -f "status=exited"
```

### Volumes

Volumes store persistent data (databases, files, etc.)

**CAUTION:** Volumes may contain important data!

```bash
# List volumes
docker volume ls

# Inspect a volume
docker volume inspect <volume_name>
```

Before pruning volumes, check if they contain data you need.

### Build Cache

Cached layers from `docker build` commands.

**Safe to remove:** Builds will just take longer next time.

```bash
# See build cache
docker builder du

# Prune build cache
docker builder prune
```

## Cleanup Commands Reference

### Conservative (Safest)

Remove only dangling images (untagged):

```bash
docker image prune
```

### Standard

Remove all unused images:

```bash
docker image prune -a
```

### Aggressive

Remove all unused resources:

```bash
docker system prune -a
```

### Maximum (Use with Care)

Remove everything including volumes:

```bash
docker system prune -a --volumes
```

## Preventing Docker Bloat

### 1. Set Resource Limits

Docker Desktop → Preferences → Resources:
- **Disk image size**: Limit maximum disk usage
- **Memory**: Limit RAM (default is very high)

### 2. Regular Cleanup

Add to your maintenance routine:

```bash
# Weekly cleanup
docker system prune -f
docker volume prune -f
```

### 3. Use .dockerignore

Prevent copying unnecessary files during build:

```
# .dockerignore
node_modules
.git
*.log
tmp/
```

### 4. Multi-stage Builds

Final images should be smaller:

```dockerfile
# Build stage
FROM node:18 AS build
WORKDIR /app
COPY . .
RUN npm ci && npm run build

# Production stage
FROM node:18-slim
COPY --from=build /app/dist /app
CMD ["node", "/app/index.js"]
```

### 5. Clean Up After Yourself

After finishing work on a project:

```bash
# Stop project containers
docker compose down

# Remove project images
docker compose down --rmi local

# Remove with volumes (careful!)
docker compose down -v
```

## The Nuclear Option

If Docker is completely out of control:

### Option 1: Reset Docker Desktop

Docker Desktop → Troubleshoot → Clean / Purge data

This removes **everything** and starts fresh.

### Option 2: Delete the Disk Image

```bash
# Stop Docker Desktop first
rm ~/Library/Containers/com.docker.docker/Data/vms/0/data/Docker.raw
```

Start Docker Desktop again—it will create a new empty disk image.

## Checking Results

After cleanup:

```bash
docker system df
```

And verify with MacCleaner:

```
/mac-cleaner:disk-audit
```

## Common Issues

### "Disk image too large"

Docker's disk image doesn't shrink automatically. After cleanup:

1. Docker Desktop → Preferences → Resources
2. Reduce "Disk image size"
3. Apply changes

Or reset Docker completely.

### "Cannot delete image: in use by container"

Stop and remove the container first:

```bash
docker stop <container_id>
docker rm <container_id>
docker rmi <image_id>
```

### "Volume in use"

Stop containers using the volume:

```bash
docker compose down
docker volume rm <volume_name>
```
