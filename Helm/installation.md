# Helm Installation Guide

## Script Installation
```bash
curl https://raw.githubusercontent.com/helm/helm/main/scripts/get-helm-3 | bash
```

## Binary Installation
```bash
# Download
wget https://get.helm.sh/helm-v3.10.0-linux-amd64.tar.gz

# Extract
tar -zxvf helm-v3.10.0-linux-amd64.tar.gz

# Move to PATH
sudo mv linux-amd64/helm /usr/local/bin/helm

# Verify
helm version
```

## Package Manager
```bash
# Ubuntu/Debian
curl https://baltocdn.com/helm/signing.asc | sudo apt-key add -
echo "deb https://baltocdn.com/helm/stable/debian/ all main" | sudo tee /etc/apt/sources.list.d/helm-stable-debian.list
sudo apt update
sudo apt install helm

# CentOS/RHEL
sudo yum install helm
```

## Basic Usage
```bash
# Add repository
helm repo add stable https://charts.helm.sh/stable
helm repo update

# Search charts
helm search repo nginx

# Install chart
helm install my-nginx stable/nginx-ingress

# List releases
helm list

# Upgrade release
helm upgrade my-nginx stable/nginx-ingress

# Uninstall
helm uninstall my-nginx
```

## Create Custom Chart
```bash
# Create new chart
helm create mychart

# Package chart
helm package mychart

# Install local chart
helm install myapp ./mychart
```
