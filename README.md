
# 🖥️ SSH Remote Server Setup

This project demonstrates how to securely configure a remote **SSH server on Ubuntu** using **public key authentication**, **custom ports**, and **Fail2Ban** to protect against brute-force attacks.

The setup was performed using **traditional port forwarding**, allowing secure remote access to a local Ubuntu machine from another device.

---

# 📌 Project Overview

This project simulates a **basic infrastructure setup commonly used in DevOps and system administration**.

It includes:

- Installing and configuring **OpenSSH Server**
- Creating and using **SSH key pairs**
- Disabling insecure authentication methods
- Changing the **default SSH port**
- Implementing **Fail2Ban** for brute-force protection
- Monitoring SSH authentication logs

This type of setup is commonly used for:

- Remote server administration
- Infrastructure management
- Cloud VM access
- DevOps environments
- Secure home lab setups

---

# ⚙️ Prerequisites

Before starting, ensure you have:

1. **Ubuntu machine**  
   (Example: Ubuntu 24.04 LTS)

2. **Client machine**  
   (Windows, Linux, or macOS)

3. **SSH Key Pair**

4. **Network access**  
   - Local network connection  
   - Router port forwarding configured (if external access is needed)

---

# 🚀 Setup Steps

## 1️⃣ Install OpenSSH Server

Update packages:

```bash
sudo apt update
```

Install OpenSSH:

```bash
sudo apt install openssh-server
```

Verify service:

```bash
sudo systemctl status ssh
```

---

## 2️⃣ Generate SSH Key Pair (Client Machine)

Example using Windows PowerShell:

```powershell
ssh-keygen -t ed25519 -C "AspiringDevOpsEngineer" -f C:\Users\solve\.ssh\devops_vm_key
```

Explanation:

| Parameter | Description |
|----------|-------------|
| `-t ed25519` | Secure modern key type |
| `-C` | Comment identifier |
| `-f` | File location for key |

Files created:

```
devops_vm_key
devops_vm_key.pub
```

---

## 3️⃣ Add Public Key to Server

Display the key:

```powershell
type C:\Users\solve\.ssh\devops_vm_key.pub
```

Copy to server:

```powershell
type %USERPROFILE%\.ssh\devops_vm_key.pub | ssh juanito@192.168.1.15 "mkdir -p ~/.ssh && cat >> ~/.ssh/authorized_keys"
```

---

## 4️⃣ Configure SSH Permissions

On the Ubuntu server:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

These permissions are required for SSH security.

---

## 5️⃣ Configure SSH Server

Edit configuration:

```bash
sudo nano /etc/ssh/sshd_config
```

Modify:

```
Port 2222
PermitRootLogin no
PasswordAuthentication no
PubkeyAuthentication yes
AuthorizedKeysFile .ssh/authorized_keys
```

Explanation:

| Setting | Purpose |
|--------|--------|
| Port 2222 | Avoid default port scans |
| PermitRootLogin no | Disable root login |
| PasswordAuthentication no | Enforce key authentication |
| PubkeyAuthentication yes | Enable key login |
| AuthorizedKeysFile | Trusted key location |

---

## 6️⃣ Restart SSH Service

```bash
sudo systemctl restart ssh
```

---

## 7️⃣ Test SSH Connection

From the client machine:

```bash
ssh -i C:\Users\solve\.ssh\devops_vm_key -p 2222 username@192.168.X.X
```

Parameters:

| Option | Description |
|------|-------------|
| `-i` | Private key path |
| `-p` | SSH port |
| `username@IP` | Remote login |

---

# 🛡️ Security Enhancement: Fail2Ban

Fail2Ban blocks IP addresses that repeatedly fail authentication.

## Install

```bash
sudo apt update
sudo apt install fail2ban -y
```

---

## Create Configuration

```bash
sudo nano /etc/fail2ban/jail.local
```

Add:

```
[DEFAULT]
bantime = 1h
findtime = 10m
maxretry = 3
banaction = iptables-multiport

[sshd]
enabled = true
port = 2222
filter = sshd-aggressive
logpath = /var/log/auth.log
maxretry = 3
```

---

## Enable and Start

```bash
sudo systemctl enable fail2ban
sudo systemctl start fail2ban
```

---

## Check Status

```bash
sudo fail2ban-client status sshd
```

Expected output:

```
Status for the jail: sshd
|- Filter
|  |- Currently failed: 0
|  |- Total failed: 0
|  `- File list: /var/log/auth.log
`- Actions
   |- Currently banned: 0
   |- Total banned: 0
   `- Banned IP list:
```

---

# 🔎 Monitoring & Troubleshooting

View SSH logs:

```bash
sudo journalctl -u ssh
```

Recent logs:

```bash
sudo journalctl -u ssh --since "5 minutes ago"
```

View Fail2Ban logs:

```bash
sudo tail -f /var/log/fail2ban.log
```

Unban IP:

```bash
sudo fail2ban-client set sshd unbanip <IP_ADDRESS>
```

---

# 🌐 Network Architecture

Example structure:

```
Client Device
     │
     │ SSH (Port 2222)
     │
Router (Port Forwarding)
     │
Ubuntu Server (OpenSSH + Fail2Ban)
```

---

# 📚 Lessons Learned

This project demonstrates:

- SSH key authentication
- Secure remote server configuration
- File permission management
- Brute force attack mitigation
- Log monitoring for security events

---

# 👤 Author

**Juanito M. Ramos II**

GitHub: https://github.com/Juaaanits

---

# 📜 License

MIT License

---

# ✅ Key Improvements

Security improvements implemented:

- Disabled password authentication
- Disabled root SSH login
- Custom SSH port
- SSH key authentication
- Fail2Ban protection
- Authentication log monitoring
