
# Generating SSH Key for Git Commit Signing (with GPG Fallback)

This guide explains how to generate and configure an **SSH key** for signing Git commits.  
If SSH signing does not work, fallback instructions for **GPG key signing** are also provided.

---

##  Generate and Configure SSH Key

### 1. Generate SSH Key
Run the following command, replacing your email:
```bash
ssh-keygen -t ed25519 -C "fname.lname@ayanworks.com"
````

### 2. Verify SSH Keys

```bash
ls -al ~/.ssh
```

### 3. Start SSH Agent

```bash
eval "$(ssh-agent -s)"
```

### 4. Add SSH Key to Agent

```bash
ssh-add ~/.ssh/id_ed25519
```

### 5. Copy SSH Public Key

```bash
cat ~/.ssh/id_ed25519.pub
```

* Copy the output.
* Go to **GitHub → Settings → SSH and GPG keys → New SSH key**.
* Paste your key and save.

---

##  Configure Git for SSH Signing

### 6. Set Git to Use SSH Format

```bash
git config --global gpg.format ssh
```

### 7. Set SSH Key for Commit Signing

```bash
git config --global user.signingkey ~/.ssh/id_ed25519
```

---

##  Signing Commits with SSH Key

Use the following command when committing:

```bash
git commit -S -s -m "your_commit_message"
```

* `-S` → Sign commit
* `-s` → Add Signed-off-by line

---

