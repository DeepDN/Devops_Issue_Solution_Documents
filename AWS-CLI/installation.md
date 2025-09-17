# AWS CLI Installation Guide

## Linux Installation

### Method 1: Installer (Recommended)
```bash
# Download installer
curl "https://awscli.amazonaws.com/awscli-exe-linux-x86_64.zip" -o "awscliv2.zip"

# Unzip
unzip awscliv2.zip

# Install
sudo ./aws/install

# Verify
aws --version
```

### Method 2: Package Manager
```bash
# Ubuntu/Debian
sudo apt update
sudo apt install awscli

# CentOS/RHEL
sudo yum install awscli
```

### Method 3: pip
```bash
# Install pip if not available
sudo apt install python3-pip

# Install AWS CLI
pip3 install awscli --upgrade --user

# Add to PATH
echo 'export PATH=$PATH:$HOME/.local/bin' >> ~/.bashrc
source ~/.bashrc
```

## Configuration

### Basic Configuration
```bash
aws configure
```
Enter:
- AWS Access Key ID
- AWS Secret Access Key  
- Default region (e.g., us-west-2)
- Default output format (json, text, table)

### Multiple Profiles
```bash
# Create named profile
aws configure --profile production

# Use profile
aws s3 ls --profile production

# Set default profile
export AWS_PROFILE=production
```

### Configuration Files
```bash
# Credentials file location
~/.aws/credentials

# Config file location  
~/.aws/config
```

Example `~/.aws/credentials`:
```ini
[default]
aws_access_key_id = AKIAIOSFODNN7EXAMPLE
aws_secret_access_key = wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY

[production]
aws_access_key_id = AKIAI44QH8DHBEXAMPLE
aws_secret_access_key = je7MtGbClwBF/2Zp9Utk/h3yCo8nvbEXAMPLEKEY
```

Example `~/.aws/config`:
```ini
[default]
region = us-west-2
output = json

[profile production]
region = us-east-1
output = table
```

## Environment Variables
```bash
export AWS_ACCESS_KEY_ID=AKIAIOSFODNN7EXAMPLE
export AWS_SECRET_ACCESS_KEY=wJalrXUtnFEMI/K7MDENG/bPxRfiCYEXAMPLEKEY
export AWS_DEFAULT_REGION=us-west-2
```

## IAM Roles (EC2)
```bash
# No configuration needed if using IAM roles
# AWS CLI automatically uses instance metadata
aws sts get-caller-identity
```

## SSO Configuration
```bash
# Configure SSO
aws configure sso

# Login
aws sso login --profile my-sso-profile
```

## Verification
```bash
# Test configuration
aws sts get-caller-identity

# List S3 buckets
aws s3 ls

# Check configuration
aws configure list
```
