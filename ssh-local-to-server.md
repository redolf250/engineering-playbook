# 🔐 Passwordless SSH Setup (Local Machine → Server)

This guide explains how to configure SSH key-based authentication so you can connect to your server **without a password**.

---

## 🧭 Step 1: Generate SSH Key (on Local Machine)

Open your terminal on your **local machine** and run:

```bash
ssh-keygen -t ed25519 -C "my-local-machine"
```

When prompted:

```text
Enter file in which to save the key (/home/your-user/.ssh/id_ed25519):
```

Press **Enter** to use the default, or specify a custom name:

```bash
~/.ssh/my_server_key
```

This creates:

```bash
~/.ssh/my_server_key        # Private key
~/.ssh/my_server_key.pub    # Public key
```

---

## 🔓 Step 2: Copy Public Key to Server

### ✅ Option 1 (Recommended: automatic)

```bash
ssh-copy-id -i ~/.ssh/my_server_key.pub root@YOUR_SERVER_IP
```

---

### ✅ Option 2 (Manual method)

1. Copy the public key:

```bash
cat ~/.ssh/my_server_key.pub
```

2. SSH into your server (with password):

```bash
ssh root@YOUR_SERVER_IP
```

3. Add key to authorized keys:

```bash
mkdir -p ~/.ssh
nano ~/.ssh/authorized_keys
```

Paste the key, then save.

---

## 🔐 Step 3: Set Correct Permissions (on Server)

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## ✅ Step 4: Test Passwordless Login

From your local machine:

```bash
ssh -i ~/.ssh/my_server_key root@YOUR_SERVER_IP
```

👉 You should log in **without being asked for a password**

---

## ⚡ Optional: Simplify SSH Command

Edit your SSH config file:

```bash
nano ~/.ssh/config
```

Add:

```text
Host my-server
  HostName YOUR_SERVER_IP
  User root
  IdentityFile ~/.ssh/my_server_key
```

Now you can connect using:

```bash
ssh my-server
```

---

## ⚠️ Security Best Practices

* Never share your **private key**
* Disable password login after setup (optional but recommended):

  ```bash
  sudo nano /etc/ssh/sshd_config
  ```

  Set:

  ```text
  PasswordAuthentication no
  ```

  Then restart SSH:

  ```bash
  sudo systemctl restart ssh
  ```

---

## 🚀 Result

You now have:

* ✅ Secure login via SSH key
* ✅ No password required
* ✅ Faster and safer server access

---
