# Jenkins Installation Guide

## Ubuntu/Debian

### Method 1: Package Manager
```bash
# Update system
sudo apt update

# Install Java
sudo apt install openjdk-11-jdk

# Add Jenkins repository
curl -fsSL https://pkg.jenkins.io/debian-stable/jenkins.io.key | sudo tee /usr/share/keyrings/jenkins-keyring.asc > /dev/null
echo deb [signed-by=/usr/share/keyrings/jenkins-keyring.asc] https://pkg.jenkins.io/debian-stable binary/ | sudo tee /etc/apt/sources.list.d/jenkins.list > /dev/null

# Install Jenkins
sudo apt update
sudo apt install jenkins

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

### Method 2: Docker
```bash
# Create volume for Jenkins data
docker volume create jenkins_home

# Run Jenkins container
docker run -d \
  --name jenkins \
  -p 8080:8080 \
  -p 50000:50000 \
  -v jenkins_home:/var/jenkins_home \
  jenkins/jenkins:lts
```

## CentOS/RHEL

```bash
# Install Java
sudo yum install java-11-openjdk-devel

# Add Jenkins repository
sudo wget -O /etc/yum.repos.d/jenkins.repo https://pkg.jenkins.io/redhat-stable/jenkins.repo
sudo rpm --import https://pkg.jenkins.io/redhat-stable/jenkins.io.key

# Install Jenkins
sudo yum install jenkins

# Start Jenkins
sudo systemctl start jenkins
sudo systemctl enable jenkins
```

## Initial Setup

### Access Jenkins
1. Open browser: `http://localhost:8080`
2. Get initial admin password:
```bash
sudo cat /var/lib/jenkins/secrets/initialAdminPassword
```

### Configure Firewall
```bash
# Ubuntu/Debian
sudo ufw allow 8080

# CentOS/RHEL
sudo firewall-cmd --permanent --add-port=8080/tcp
sudo firewall-cmd --reload
```

## Jenkins with SSL

### Generate SSL Certificate
```bash
# Create keystore
keytool -genkey -keyalg RSA -alias jenkins -keystore jenkins.jks -keysize 2048

# Configure Jenkins
sudo systemctl edit jenkins
```

Add to override file:
```ini
[Service]
Environment="JENKINS_OPTS=--httpPort=-1 --httpsPort=8443 --httpsKeyStore=/path/to/jenkins.jks --httpsKeyStorePassword=password"
```

## Jenkins with Nginx Reverse Proxy

### Install Nginx
```bash
sudo apt install nginx
```

### Configure Nginx
```bash
sudo nano /etc/nginx/sites-available/jenkins
```

```nginx
server {
    listen 80;
    server_name jenkins.example.com;
    
    location / {
        proxy_pass http://localhost:8080;
        proxy_set_header Host $host;
        proxy_set_header X-Real-IP $remote_addr;
        proxy_set_header X-Forwarded-For $proxy_add_x_forwarded_for;
        proxy_set_header X-Forwarded-Proto $scheme;
    }
}
```

```bash
sudo ln -s /etc/nginx/sites-available/jenkins /etc/nginx/sites-enabled/
sudo nginx -t
sudo systemctl restart nginx
```
