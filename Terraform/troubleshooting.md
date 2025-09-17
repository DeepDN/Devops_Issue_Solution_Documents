# Terraform Troubleshooting Guide

## State File Issues

### Error: `state lock`
**Cause:** Previous operation didn't complete properly

**Solution:**
```bash
# Force unlock (use with caution)
terraform force-unlock <lock-id>

# Or wait for lock to expire
# Check backend configuration for timeout settings
```

### Error: `state file not found`
**Cause:** State file deleted or moved

**Solution:**
```bash
# Import existing resources
terraform import aws_instance.example i-1234567890abcdef0

# Or reinitialize
terraform init
```

## Provider Issues

### Error: `provider not found`
**Cause:** Provider not installed or wrong version

**Solution:**
```bash
# Reinitialize
terraform init

# Upgrade providers
terraform init -upgrade

# Lock provider version in versions.tf
terraform {
  required_providers {
    aws = {
      source  = "hashicorp/aws"
      version = "~> 4.0"
    }
  }
}
```

### Error: `authentication failed`
**Cause:** Invalid credentials

**Solution:**
```bash
# Check AWS credentials
aws configure list

# Set environment variables
export AWS_ACCESS_KEY_ID="your-key"
export AWS_SECRET_ACCESS_KEY="your-secret"

# Or use AWS profile
export AWS_PROFILE="your-profile"
```

## Resource Issues

### Error: `resource already exists`
**Cause:** Resource exists but not in state

**Solution:**
```bash
# Import existing resource
terraform import aws_instance.example i-1234567890abcdef0

# Or remove from AWS and recreate
terraform apply
```

### Error: `dependency cycle`
**Cause:** Circular dependencies between resources

**Solution:**
```bash
# Use depends_on explicitly
resource "aws_instance" "example" {
  depends_on = [aws_security_group.example]
}

# Or restructure resources to break cycle
```

## Plan/Apply Issues

### Error: `no changes detected`
**Cause:** State drift or configuration issues

**Solution:**
```bash
# Refresh state
terraform refresh

# Force replacement
terraform apply -replace=aws_instance.example

# Check for drift
terraform plan -detailed-exitcode
```

### Error: `timeout waiting for state`
**Cause:** Resource taking too long to create/update

**Solution:**
```bash
# Increase timeout in resource
resource "aws_instance" "example" {
  timeouts {
    create = "10m"
    update = "10m"
    delete = "10m"
  }
}
```

## Backend Issues

### Error: `backend initialization failed`
**Cause:** Backend configuration issues

**Solution:**
```bash
# Reconfigure backend
terraform init -reconfigure

# Migrate state
terraform init -migrate-state

# Check backend configuration
terraform {
  backend "s3" {
    bucket = "my-terraform-state"
    key    = "terraform.tfstate"
    region = "us-west-2"
  }
}
```

## Performance Issues

### Slow plan/apply
**Solution:**
```bash
# Use parallelism
terraform apply -parallelism=10

# Target specific resources
terraform apply -target=aws_instance.example

# Use smaller state files (workspaces)
terraform workspace new production
```

## Debugging

### Enable detailed logging
```bash
export TF_LOG=DEBUG
export TF_LOG_PATH=terraform.log
terraform apply
```

### Common debug commands
```bash
# Validate configuration
terraform validate

# Format code
terraform fmt

# Show current state
terraform show

# List resources in state
terraform state list

# Show specific resource
terraform state show aws_instance.example
```
