Absolutely! Here’s a **comprehensive step-by-step guide** for generating an SSH key, connecting to a Linux server, and configuring it to **only accept SSH key authentication (password login disabled)**. This is written as a clear document you can save or share.

---

# 🔒 SSH Key Setup and Passwordless Login Guide

## 1. Generate an SSH Key Pair (on your local machine)

SSH keys consist of a **private key** (kept secret) and a **public key** (shared with the server).

### Windows (PowerShell, Git Bash, or WSL)

```bash
ssh-keygen -t ed25519 -C "your_email@example.com"
```

* `-t ed25519` → modern, secure algorithm
* `-C "your_email@example.com"` → optional comment for identifying the key
* Save location: Press **Enter** to accept default (`~/.ssh/id_ed25519`)
* Passphrase: optional but recommended for added security

This generates:

| File             | Purpose                     |
| ---------------- | --------------------------- |
| `id_ed25519`     | Private key (keep safe)     |
| `id_ed25519.pub` | Public key (copy to server) |

---

## 2. Copy the Public Key to the Server

You need to place your public key in the server user’s `~/.ssh/authorized_keys` file.

### Option 1: Using `ssh-copy-id` (if password login is allowed)

```bash
ssh-copy-id -i ~/.ssh/id_ed25519.pub user@your_server_ip
```

### Option 2: Manual method

1. Copy your public key content:

```bash
cat ~/.ssh/id_ed25519.pub
```

2. SSH into server using password:

```bash
ssh user@your_server_ip
```

3. Ensure `.ssh` folder exists and has correct permissions:

```bash
mkdir -p ~/.ssh
chmod 700 ~/.ssh
```

4. Append your public key:

```bash
echo "your_public_key_here" >> ~/.ssh/authorized_keys
chmod 600 ~/.ssh/authorized_keys
```

> **Permissions explanation:**
>
> * `.ssh` → `700` (only owner can read/write/execute)
> * `authorized_keys` → `600` (only owner can read/write)
> * Private key on client → `600` (only owner can read/write)
> * Public key → `644` (readable by others is okay)

---

## 3. Test SSH Key Authentication

From your local machine:

```bash
ssh -i ~/.ssh/id_ed25519 user@your_server_ip
```

* If it connects without asking for a password → SSH key works.
* If it fails → check that the public key is correct in `authorized_keys` and permissions are correct.

---

## 4. Disable Password Login on the Server

### 4.1 Backup SSH configuration

```bash
sudo cp /etc/ssh/sshd_config /etc/ssh/sshd_config.bak
```

### 4.2 Edit SSH config

```bash
sudo nano /etc/ssh/sshd_config
```

Set the following:

```text
PubkeyAuthentication yes
PasswordAuthentication no
ChallengeResponseAuthentication no
PermitRootLogin prohibit-password
```

* **Important:** Some distros include additional config files:

```text
Include /etc/ssh/sshd_config.d/*.conf
```

Check these included files and make sure `PasswordAuthentication` is set to `no`.

```bash
grep -Ri passwordauthentication /etc/ssh/sshd_config.d/
```

---

### 4.3 Restart SSH service

```bash
sudo systemctl restart ssh
```

or

```bash
sudo systemctl restart sshd
```

---

### 4.4 Verify effective settings

```bash
sudo sshd -T | grep passwordauthentication
```

* Should output:

```
passwordauthentication no
```

---

## 5. Test Before Closing Current Session

* **Open a new terminal** and connect using your key:

```bash
ssh -i ~/.ssh/id_ed25519 user@your_server_ip
```

* If it works, password login is disabled safely.
* If it fails, restore backup config:

```bash
sudo cp /etc/ssh/sshd_config.bak /etc/ssh/sshd_config
sudo systemctl restart ssh
```

---

## 6. Optional: Secure SSH Further

* Change default SSH port (`Port 22 → 11433` for example)
* Disable root login entirely: `PermitRootLogin no`
* Use fail2ban to block repeated failed attempts
* Only allow specific IPs via firewall rules (optional for extra security)

---

✅ **Result:**

* Server only allows SSH connections from clients with **valid private keys**.
* Password login is fully disabled, preventing brute-force attacks.

---

If you want, I can also make a **diagram showing the flow of private key → public key → authorized_keys → SSH login**, which makes it very easy to explain and share.

Do you want me to create that diagram?
