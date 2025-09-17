# Nginx Troubleshooting Guide

## Service Issues

### Error: `nginx: [emerg] bind() to 0.0.0.0:80 failed`
**Cause:** Port already in use

**Solution:**
```bash
# Find process using port 80
sudo netstat -tulpn | grep :80
sudo lsof -i :80

# Kill process or change nginx port
sudo kill -9 PID
```

### Error: `nginx: [emerg] open() "/var/log/nginx/access.log" failed`
**Solution:**
```bash
# Create log directory
sudo mkdir -p /var/log/nginx
sudo chown www-data:www-data /var/log/nginx
```

## Configuration Issues

### Error: `nginx: [emerg] "server" directive is not allowed here`
**Solution:**
```bash
# Check configuration syntax
sudo nginx -t

# Ensure server blocks are in http context
http {
    server {
        # server configuration
    }
}
```

### Error: `403 Forbidden`
**Solution:**
```bash
# Check file permissions
sudo chmod 755 /var/www/html
sudo chmod 644 /var/www/html/index.html

# Check nginx user
ps aux | grep nginx

# Fix ownership
sudo chown -R www-data:www-data /var/www/html
```

## SSL Issues

### Error: `SSL certificate problem`
**Solution:**
```bash
# Check certificate
openssl x509 -in /path/to/cert.pem -text -noout

# Test SSL configuration
sudo nginx -t
openssl s_client -connect domain.com:443
```

## Performance Issues

### High CPU usage
**Solution:**
```bash
# Optimize worker processes
worker_processes auto;
worker_connections 1024;

# Enable gzip compression
gzip on;
gzip_types text/plain text/css application/json;
```

### Memory issues
**Solution:**
```bash
# Limit client body size
client_max_body_size 10M;

# Optimize buffers
client_body_buffer_size 128k;
client_header_buffer_size 1k;
```
