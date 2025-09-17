
# Generating a New GPG Key in Linux

GPG (GNU Privacy Guard) allows you to sign commits and tags to verify authenticity. This guide explains how to generate and configure a new GPG key in Linux for use with GitHub.

---

## Prerequisites

- Ensure your **email address is verified** on GitHub.  
  If not, you won’t be able to sign commits or tags with GPG.  
  - [Verifying your email address](https://docs.github.com/en/account-and-profile/setting-up-and-managing-your-github-user-account/managing-email-preferences/adding-an-email-address-to-your-github-account)

- Install the **latest GPG tools** for your operating system.

---

## Steps to Generate a GPG Key

### 1. Open Terminal  
Run the following command to generate a new GPG key pair:

For **GPG v2.1.17 or later**:
```bash
gpg --full-generate-key
````

For **older versions**:

```bash
gpg --default-new-key-algo rsa4096 --gen-key
```

---

### 2. Configure Key Options

* Select the **key type** (default is usually fine).
* Choose the **key size** (recommended: `4096`).
* Set an **expiration** (default: never expires).
* Confirm your selections.

---

### 3. Enter User Information

* Provide your **name** and **verified GitHub email address**.
* If you want to keep your personal email private, use GitHub’s **no-reply email address**.
* Set a **strong passphrase** for security.

---

### 4. List GPG Keys

Run the following command to see the long format key IDs:

```bash
gpg --list-secret-keys --keyid-format=long
```

 **Note:** Some Linux installations may require:

```bash
gpg2 --list-keys --keyid-format LONG
```

If so, configure Git to use `gpg2`:

```bash
git config --global gpg.program gpg2
```

---

### 5. Copy the Key ID

Example output:

```
/home/user/.gnupg/secring.gpg
------------------------------------
sec   4096R/3AA5C34371567BD2 2016-03-10 [expires: 2017-03-10]
uid                          John Doe <john@example.com>
ssb   4096R/4BB6D45482678BE3 2016-03-10
```

In this case, the **GPG key ID** is `3AA5C34371567BD2`.

---

### 6. Export Your Public Key

Replace `<KEY_ID>` with your copied GPG key ID:

```bash
gpg --armor --export <KEY_ID>
```

This prints your GPG public key in ASCII format:

```
-----BEGIN PGP PUBLIC KEY BLOCK-----
...
-----END PGP PUBLIC KEY BLOCK-----
```

Copy the entire block.

---

### 7. Add Key to GitHub

* Go to **GitHub → Settings → SSH and GPG keys → New GPG key**
* Paste your copied GPG key.

---

## Configure Git with GPG

Unset any old key format configuration:

```bash
git config --global --unset gpg.format
```

Set your primary GPG signing key:

```bash
git config --global user.signingkey <KEY_ID>
```

Enable commit signing by default:

```bash
git config --global commit.gpgsign true
```

Ensure `GPG_TTY` is set by adding this to your `~/.bashrc`:

```bash
[ -f ~/.bashrc ] && echo 'export GPG_TTY=$(tty)' >> ~/.bashrc
```

Then reload your shell:

```bash
source ~/.bashrc
```

---

## Signing Commits

When committing, use:

```bash
git commit -S -s -m "your_commit_message"
```

* `-S` → Sign commit with GPG key
* `-s` → Add Signed-off-by line

---
