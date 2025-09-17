# Ansible Installation Guide

## Ubuntu/Debian
```bash
sudo apt update
sudo apt install software-properties-common
sudo add-apt-repository --yes --update ppa:ansible/ansible
sudo apt install ansible
```

## CentOS/RHEL
```bash
sudo yum install epel-release
sudo yum install ansible
```

## pip Installation
```bash
pip3 install ansible
```

## Verification
```bash
ansible --version
ansible-playbook --version
```

## Configuration
```bash
# Create inventory
echo "[servers]" > inventory
echo "server1 ansible_host=192.168.1.10" >> inventory

# Test connection
ansible all -i inventory -m ping
```
