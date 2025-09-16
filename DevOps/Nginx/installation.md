# Nginx Installation Guide

## Ubuntu/Debian
```bash
sudo apt update
sudo apt install nginx

# Start and enable
sudo systemctl start nginx
sudo systemctl enable nginx
```

## CentOS/RHEL
```bash
sudo yum install epel-release
sudo yum install nginx

# Start and enable
sudo systemctl start nginx
sudo systemctl enable nginx
```

## From Source
```bash
# Install dependencies
sudo apt install build-essential libpcre3-dev libssl-dev zlib1g-dev

# Download and compile
wget http://nginx.org/download/nginx-1.20.2.tar.gz
tar -xzf nginx-1.20.2.tar.gz
cd nginx-1.20.2

./configure --with-http_ssl_module --with-http_v2_module
make
sudo make install
```

## Docker Installation
```bash
docker run -d \
  --name nginx \
  -p 80:80 \
  -v /path/to/html:/usr/share/nginx/html \
  nginx:alpine
```

## Basic Configuration
```bash
# Test configuration
sudo nginx -t

# Reload configuration
sudo nginx -s reload

# Main config file
sudo nano /etc/nginx/nginx.conf
```

## Firewall
```bash
# Ubuntu
sudo ufw allow 'Nginx Full'

# CentOS
sudo firewall-cmd --permanent --add-service=http
sudo firewall-cmd --permanent --add-service=https
sudo firewall-cmd --reload
```
