# Redis Installation Guide

## Ubuntu/Debian
```bash
sudo apt update
sudo apt install redis-server

# Start and enable
sudo systemctl start redis-server
sudo systemctl enable redis-server
```

## CentOS/RHEL
```bash
sudo yum install epel-release
sudo yum install redis

# Start and enable
sudo systemctl start redis
sudo systemctl enable redis
```

## From Source
```bash
# Install dependencies
sudo apt install build-essential tcl

# Download and compile
wget http://download.redis.io/redis-stable.tar.gz
tar xzf redis-stable.tar.gz
cd redis-stable
make
sudo make install
```

## Docker Installation
```bash
docker run -d \
  --name redis \
  -p 6379:6379 \
  -v redis_data:/data \
  redis:alpine redis-server --appendonly yes
```

## Configuration
```bash
# Edit config
sudo nano /etc/redis/redis.conf

# Key settings
bind 127.0.0.1
port 6379
requirepass yourpassword
maxmemory 256mb
maxmemory-policy allkeys-lru
```

## Security
```bash
# Set password
redis-cli
CONFIG SET requirepass "yourpassword"

# Disable dangerous commands
rename-command FLUSHDB ""
rename-command FLUSHALL ""
```

## Testing
```bash
# Connect to Redis
redis-cli

# Test commands
ping
set test "hello"
get test
```
