# Ansible Troubleshooting Guide

## SSH Connection Issues

### Error: `SSH connection failed`
**Solution:**
```bash
# Test SSH manually
ssh user@host

# Use SSH key
ansible all -i inventory --private-key ~/.ssh/id_rsa -m ping

# Skip host key checking
export ANSIBLE_HOST_KEY_CHECKING=False
```

## Permission Issues

### Error: `sudo password required`
**Solution:**
```bash
# Prompt for sudo password
ansible-playbook playbook.yml --ask-become-pass

# Use passwordless sudo
echo "user ALL=(ALL) NOPASSWD:ALL" | sudo tee /etc/sudoers.d/user
```

## Module Issues

### Error: `module not found`
**Solution:**
```bash
# Install collection
ansible-galaxy collection install community.general

# List installed modules
ansible-doc -l
```

## Inventory Issues

### Error: `host not found`
**Solution:**
```bash
# Verify inventory syntax
ansible-inventory --list

# Test specific host
ansible server1 -i inventory -m ping
```
