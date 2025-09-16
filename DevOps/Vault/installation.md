# HashiCorp Vault Installation Guide

## Binary Installation
```bash
# Download
wget https://releases.hashicorp.com/vault/1.12.0/vault_1.12.0_linux_amd64.zip

# Extract
unzip vault_1.12.0_linux_amd64.zip

# Move to PATH
sudo mv vault /usr/local/bin/

# Verify
vault version
```

## Package Manager
```bash
# Ubuntu/Debian
curl -fsSL https://apt.releases.hashicorp.com/gpg | sudo apt-key add -
sudo apt-add-repository "deb [arch=amd64] https://apt.releases.hashicorp.com $(lsb_release -cs) main"
sudo apt update && sudo apt install vault

# CentOS/RHEL
sudo yum install -y yum-utils
sudo yum-config-manager --add-repo https://rpm.releases.hashicorp.com/RHEL/hashicorp.repo
sudo yum install vault
```

## Docker Installation
```bash
docker run -d \
  --name vault \
  --cap-add=IPC_LOCK \
  -p 8200:8200 \
  -v vault_data:/vault/data \
  vault:latest
```

## Configuration
```bash
# Create config file
sudo mkdir -p /etc/vault
sudo tee /etc/vault/config.hcl > /dev/null <<EOF
storage "file" {
  path = "/opt/vault/data"
}

listener "tcp" {
  address     = "0.0.0.0:8200"
  tls_disable = 1
}

ui = true
EOF
```

## Initialize Vault
```bash
# Start server
vault server -config=/etc/vault/config.hcl

# Initialize (in new terminal)
export VAULT_ADDR='http://127.0.0.1:8200'
vault operator init

# Unseal vault (use 3 of 5 keys)
vault operator unseal <key1>
vault operator unseal <key2>
vault operator unseal <key3>

# Login with root token
vault auth <root_token>
```

## Systemd Service
```bash
sudo tee /etc/systemd/system/vault.service > /dev/null <<EOF
[Unit]
Description=Vault
Documentation=https://www.vaultproject.io/
Requires=network-online.target
After=network-online.target

[Service]
Type=notify
User=vault
Group=vault
ExecStart=/usr/local/bin/vault server -config=/etc/vault/config.hcl
ExecReload=/bin/kill -HUP $MAINPID
KillMode=process
Restart=on-failure
LimitNOFILE=65536

[Install]
WantedBy=multi-user.target
EOF

sudo systemctl enable vault
sudo systemctl start vault
```
