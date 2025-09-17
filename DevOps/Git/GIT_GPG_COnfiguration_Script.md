# Script: Install Git + GPG and Configure Signing

Save this as setup-git-gpg.sh and run with bash setup-git-gpg.sh

```bash
#!/bin/bash
set -e

# -------------------------------
# 1. Fix sources.list for Ubuntu 24.04
# -------------------------------
echo "[INFO] Updating APT sources for Ubuntu Noble..."
sudo tee /etc/apt/sources.list > /dev/null << 'EOF'
deb http://archive.ubuntu.com/ubuntu noble main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu noble-updates main restricted universe multiverse
deb http://archive.ubuntu.com/ubuntu noble-backports main restricted universe multiverse
deb http://security.ubuntu.com/ubuntu noble-security main restricted universe multiverse
EOF

# -------------------------------
# 2. Update system
# -------------------------------
echo "[INFO] Updating system..."
sudo apt update -y
sudo apt upgrade -y

# -------------------------------
# 3. Install Git + GPG
# -------------------------------
echo "[INFO] Installing Git and GnuPG..."
sudo apt install -y git gnupg2 pinentry-gtk2

# -------------------------------
# 4. Configure Git identity
# -------------------------------
GIT_NAME="Deepak Nemade"
GIT_EMAIL="your_email@example.com"   # <-- Change this to your GitHub email

git config --global user.name "$GIT_NAME"
git config --global user.email "$GIT_EMAIL"

# -------------------------------
# 5. Generate GPG key
# -------------------------------
echo "[INFO] Generating a new GPG key..."
cat > gen-key-script <<EOF
%no-protection
Key-Type: RSA
Key-Length: 4096
Subkey-Type: RSA
Subkey-Length: 4096
Name-Real: $GIT_NAME
Name-Email: $GIT_EMAIL
Expire-Date: 0
%commit
EOF

gpg --batch --generate-key gen-key-script
rm -f gen-key-script

# -------------------------------
# 6. Find GPG key ID
# -------------------------------
KEY_ID=$(gpg --list-secret-keys --keyid-format=long "$GIT_EMAIL" | grep sec | awk '{print $2}' | cut -d'/' -f2)

if [ -z "$KEY_ID" ]; then
    echo "[ERROR] Could not find generated GPG key."
    exit 1
fi

echo "[INFO] Generated GPG key ID: $KEY_ID"

# -------------------------------
# 7. Configure Git to sign commits
# -------------------------------
git config --global user.signingkey $KEY_ID
git config --global commit.gpgsign true
git config --global gpg.program gpg

# -------------------------------
# 8. Export GPG public key
# -------------------------------
echo "[INFO] Your GPG public key (add this to GitHub):"
echo "--------------------------------------------"
gpg --armor --export $KEY_ID
echo "--------------------------------------------"

echo "[SUCCESS] Git + GPG configured successfully!"
echo " Copy the above public key and add it to GitHub under: Settings → SSH and GPG keys → New GPG key"

``` 


🔹 How to Run

Save the script:
```bash
nano setup-git-gpg.sh
```

Paste the script, save & exit.

Make it executable:
```bash
chmod +x setup-git-gpg.sh
```

Run:
```bash
./setup-git-gpg.sh
```

After running, you’ll get your GPG public key printed — copy it and add to GitHub.