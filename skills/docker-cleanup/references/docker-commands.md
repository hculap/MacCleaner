# Docker Cleanup Commands Reference

## Diagnostic Commands

### Disk Usage Overview
```bash
docker system df
```

### Detailed Disk Usage
```bash
docker system df -v
```

### Docker Info (includes storage driver)
```bash
docker info
```

## Container Cleanup

### List All Containers (including stopped)
```bash
docker ps -a
```

### List Only Stopped Containers
```bash
docker ps -a -f status=exited
```

### Remove Stopped Containers
```bash
docker container prune -f
```

### Remove Specific Container
```bash
docker rm container_name_or_id
```

### Force Remove Running Container
```bash
docker rm -f container_name_or_id
```

### Remove All Containers
```bash
docker rm -f $(docker ps -aq)
```

## Image Cleanup

### List All Images
```bash
docker images -a
```

### List Dangling Images
```bash
docker images -f dangling=true
```

### Remove Dangling Images
```bash
docker image prune -f
```

### Remove All Unused Images
```bash
docker image prune -a -f
```

### Remove Specific Image
```bash
docker rmi image_name_or_id
```

### Remove Images by Pattern
```bash
docker images | grep "pattern" | awk '{print $3}' | xargs docker rmi
```

### Remove All Images
```bash
docker rmi -f $(docker images -aq)
```

## Volume Cleanup

### List All Volumes
```bash
docker volume ls
```

### List Dangling Volumes
```bash
docker volume ls -f dangling=true
```

### Remove Unused Volumes
```bash
docker volume prune -f
```

### Inspect Volume Before Deletion
```bash
docker volume inspect volume_name
```

### Remove Specific Volume
```bash
docker volume rm volume_name
```

### Remove All Volumes (DANGER!)
```bash
docker volume rm $(docker volume ls -q)
```

## Network Cleanup

### List Networks
```bash
docker network ls
```

### Remove Unused Networks
```bash
docker network prune -f
```

## Build Cache Cleanup

### Show Build Cache
```bash
docker builder du
```

### Remove Build Cache
```bash
docker builder prune -f
```

### Remove All Build Cache
```bash
docker builder prune -a -f
```

## System-Wide Cleanup

### Basic Cleanup (Safe)
```bash
docker system prune -f
```

### Remove All Unused Resources
```bash
docker system prune -a -f
```

### Remove Everything Including Volumes
```bash
docker system prune -a --volumes -f
```

### With Filter (Keep Recent)
```bash
# Keep images/containers used in last 24h
docker system prune -a -f --filter "until=24h"
```

## Docker Compose Commands

### Stop and Remove Containers
```bash
docker compose down
```

### Also Remove Volumes
```bash
docker compose down -v
```

### Also Remove Images
```bash
docker compose down --rmi all
```

### Also Remove Orphan Containers
```bash
docker compose down --remove-orphans
```

## macOS-Specific Commands

### Check Docker Desktop Disk Image Size
```bash
ls -lh ~/Library/Containers/com.docker.docker/Data/vms/0/data/
```

### Total Docker Directory Size
```bash
du -sh ~/Library/Containers/com.docker.docker/
```

## One-Line Full Cleanup

```bash
docker system prune -a --volumes -f && docker builder prune -a -f
```

## Safe Cleanup Script

```bash
#!/bin/bash
echo "=== Docker Cleanup ==="
echo "Before:"
docker system df

docker container prune -f
docker image prune -f
docker network prune -f
docker builder prune -f

echo ""
echo "After:"
docker system df
```
