# Docker Troubleshooting Guide

## Permission Denied Errors

### Error: `permission denied while trying to connect to Docker daemon`
**Cause:** User not in docker group

**Solution:**
```bash
sudo usermod -aG docker $USER
newgrp docker
# Or logout and login again
```

## Service Issues

### Error: `Cannot connect to the Docker daemon`
**Cause:** Docker service not running

**Solution:**
```bash
sudo systemctl start docker
sudo systemctl enable docker
```

### Error: `docker: command not found`
**Cause:** Docker not installed or not in PATH

**Solution:**
```bash
# Check if installed
which docker
# If not found, reinstall Docker
```

## Storage Issues

### Error: `no space left on device`
**Cause:** Docker using too much disk space

**Solution:**
```bash
# Clean up unused containers, networks, images
docker system prune -a

# Remove specific items
docker container prune
docker image prune
docker volume prune
docker network prune
```

### Check disk usage
```bash
docker system df
```

## Network Issues

### Error: `port already in use`
**Cause:** Port conflict

**Solution:**
```bash
# Find process using port
sudo netstat -tulpn | grep :8080
# Kill process or use different port
docker run -p 8081:80 nginx
```

### Error: `network not found`
**Solution:**
```bash
# List networks
docker network ls
# Create network
docker network create mynetwork
```

## Image Issues

### Error: `pull access denied`
**Cause:** Private repository or rate limiting

**Solution:**
```bash
# Login to registry
docker login
# Or use different registry
docker pull ghcr.io/user/image
```

### Error: `image not found`
**Solution:**
```bash
# Check image name and tag
docker search imagename
# Pull with correct tag
docker pull nginx:1.21
```

## Container Issues

### Container exits immediately
**Cause:** No foreground process

**Solution:**
```bash
# Check logs
docker logs container_name
# Run with interactive mode
docker run -it ubuntu bash
```

### Error: `container name already in use`
**Solution:**
```bash
# Remove existing container
docker rm container_name
# Or use different name
docker run --name myapp2 nginx
```

## Performance Issues

### High CPU/Memory usage
**Solution:**
```bash
# Monitor resources
docker stats
# Limit resources
docker run --memory="512m" --cpus="1.0" nginx
```

### Slow build times
**Solution:**
```bash
# Use .dockerignore
echo "node_modules" > .dockerignore
# Use multi-stage builds
# Enable BuildKit
export DOCKER_BUILDKIT=1
```
