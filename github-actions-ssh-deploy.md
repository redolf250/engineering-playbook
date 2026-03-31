# 🔐 GitHub Actions SSH Setup Guide

This guide explains how to configure SSH access so GitHub Actions can securely deploy your application to a server.

---

## 🧭 Step 1: Generate SSH Key on Server

SSH into your server and navigate to the `.ssh` directory:

```bash
cd ~/.ssh
```

Generate a new SSH key:

```bash
ssh-keygen -t ed25519 -C "github-actions"
```

When prompted:

```text
Enter file in which to save the key (/home/your-user/.ssh/id_ed25519):
```

Enter a custom name (recommended):

```bash
github_actions_key
```

This will create:

```bash
~/.ssh/github_actions_key        # Private key
~/.ssh/github_actions_key.pub    # Public key
```

---

## 🔓 Step 2: Add Public Key to Authorized Keys

Allow SSH access by adding the public key to `authorized_keys`:

```bash
cat ~/.ssh/github_actions_key.pub >> ~/.ssh/authorized_keys
```

Set correct permissions:

```bash
chmod 700 ~/.ssh
chmod 600 ~/.ssh/authorized_keys
```

---

## 🔐 Step 3: Copy Private Key to GitHub

Display the private key:

```bash
cat ~/.ssh/github_actions_key
```

Copy the output, then go to your GitHub repository:

**Settings → Secrets and variables → Actions → New repository secret**

Add the following:


| Name              | Value                      |
| ----------------- | -------------------------- |
| `SSH_PRIVATE_KEY` | *(paste private key here)* |

---

## 🌐 Step 4: Add Server Credentials

Also add these secrets in GitHub:


| Name          | Description                          |
| ------------- | ------------------------------------ |
| `SERVER_IP`   | Your server IP address               |
| `SERVER_USER` | SSH username (e.g.,`root`or`ubuntu`) |

---

## ✅ Step 5: Test SSH Connection (Optional)

You can test locally:

```bash
ssh -i ~/.ssh/github_actions_key root@YOUR_SERVER_IP
```

If successful, GitHub Actions will also connect successfully.

---

## ⚠️ Security Notes

* Never share your **private key**
* Only the `.pub` key goes to the server
* Use separate keys for automation (recommended)
* Restrict permissions on `.ssh` directory

---

## 🚀 Next Step

Use this SSH setup in your GitHub Actions workflow to automate deployments.

---
