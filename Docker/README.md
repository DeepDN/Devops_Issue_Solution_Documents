# Docker Documentation

##  Contents
- [Installation Guide](./installation.md)
- [Configuration](./configuration.md)
- [Troubleshooting](./troubleshooting.md)
- [Examples](./examples/)

## Quick Reference

### Common Commands
```bash
# System info
docker version
docker info

# Container management
docker run -d --name myapp nginx
docker ps
docker stop myapp
docker rm myapp

# Image management
docker images
docker pull ubuntu:20.04
docker rmi image_id
```

### Useful Flags
- `-d` - Run in detached mode
- `-p` - Port mapping (host:container)
- `-v` - Volume mounting
- `--name` - Container name
- `--rm` - Auto-remove on exit
