# Grafana Installation Guide

## Ubuntu/Debian
```bash
# Add repository
sudo apt-get install -y software-properties-common
wget -q -O - https://packages.grafana.com/gpg.key | sudo apt-key add -
echo "deb https://packages.grafana.com/oss/deb stable main" | sudo tee -a /etc/apt/sources.list.d/grafana.list

# Install
sudo apt-get update
sudo apt-get install grafana

# Start service
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

## CentOS/RHEL
```bash
# Add repository
sudo tee /etc/yum.repos.d/grafana.repo > /dev/null <<EOF
[grafana]
name=grafana
baseurl=https://packages.grafana.com/oss/rpm
repo_gpgcheck=1
enabled=1
gpgcheck=1
gpgkey=https://packages.grafana.com/gpg.key
sslverify=1
sslcacert=/etc/pki/tls/certs/ca-bundle.crt
EOF

# Install
sudo yum install grafana

# Start service
sudo systemctl start grafana-server
sudo systemctl enable grafana-server
```

## Docker Installation
```bash
docker run -d \
  --name grafana \
  -p 3000:3000 \
  -v grafana-storage:/var/lib/grafana \
  grafana/grafana-oss
```

## Access
- URL: http://localhost:3000
- Default login: admin/admin
