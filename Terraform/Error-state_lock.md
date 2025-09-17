# Error: state lock

**Cause:** Previous operation didn't complete properly

**Solution:**
```bash
# Force unlock (use with caution)
terraform force-unlock <lock-id>

# Or wait for lock to expire
# Check backend configuration for timeout settings
