# HashiCorp Consul Installation Guide

## Binary Installation
```bash
# Download
wget https://releases.hashicorp.com/consul/1.13.0/consul_1.13.0_linux_amd64.zip

# Extract
unzip consul_1.13.0_linux_amd64.zip

# Move to PATH
sudo mv consul /usr/local/bin/

# Verify
consul version
```

## Package Manager
```bash
# Ubuntu/Debian
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt update && sudo apt install consul

# CentOS/RHEL
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum install consul
```

## Docker Installation
```bash
docker run -d \
  --name consul \
  -p 8500:8500 \
  -p 8600:8600/udp \
  consul:latest agent -server -ui -node=server-1 -bootstrap-expect=1 -client=0.0.0.0
```

## Configuration
```bash
# Create config directory
sudo mkdir -p /etc/consul.d

# Create config file
sudo tee /etc/consul.d/consul.hcl > /dev/null <<EOF
datacenter = "dc1"
data_dir = "/opt/consul"
log_level = "INFO"
server = true
bootstrap_expect = 1
bind_addr = "0.0.0.0"
client_addr = "0.0.0.0"
retry_join = ["127.0.0.1"]
ui_config {
  enabled = true
}
connect {
  enabled = true
}
EOF
```

## Start Consul
```bash
# Development mode (single node)
consul agent -dev

# Production mode
consul agent -config-dir=/etc/consul.d/
```

## Systemd Service
```bash
sudo tee /etc/systemd/system/consul.service > /dev/null <<EOF
[Unit]
Description=Consul
Documentation=https://www.consul.io/
Requires=network-online.target
After=network-online.target

[Service]
Type=notify
User=consul
Group=consul
ExecStart=/usr/local/bin/consul agent -config-dir=/etc/consul.d/
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable consul
sudo systemctl start consul
```

## Access UI
- URL: http://localhost:8500
