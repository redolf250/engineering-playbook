# 🚀 Deployment Workflow (GitHub Actions → Server)

This document explains how deployments are automated using **GitHub Actions** and **SSH**.

---

## 🧭 Overview

The deployment pipeline works as follows:

```text
Feature Branch → PR → development → auto deploy to TEST server
                              ↓
                         PR → main → auto deploy to PROD server
```

---

## 🔐 Required GitHub Secrets

Go to:

**GitHub → Settings → Secrets and variables → Actions**

Add the following:

### 🧪 Test Environment (Development)


| Name              | Description           |
| ----------------- | --------------------- |
| `DEV_SERVER_IP`   | Test server IP        |
| `DEV_SERVER_USER` | SSH user (e.g., root) |
| `DEV_SSH_KEY`     | Private SSH key       |

---

### 🚀 Production Environment


| Name               | Description          |
| ------------------ | -------------------- |
| `PROD_SERVER_IP`   | Production server IP |
| `PROD_SERVER_USER` | SSH user             |
| `PROD_SSH_KEY`     | Private SSH key      |

---

## ⚙️ GitHub Actions Workflow

Create:

```bash
.github/workflows/deploy.yml
```

### 📄 Full Workflow

```yaml
name: Deploy Environments

on:
  pull_request:
    types: [closed]

jobs:
  deploy-dev:
    if: github.event.pull_request.merged == true && github.event.pull_request.base.ref == 'development'
    runs-on: ubuntu-latest

    steps:
      - name: Deploy to DEV server
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.DEV_SERVER_IP }}
          username: ${{ secrets.DEV_SERVER_USER }}
          key: ${{ secrets.DEV_SSH_KEY }}
          script: |
            cd /root/apps/trade-sphere
            git fetch origin
            git reset --hard origin/development
            npm ci --omit=dev
            pm2 restart trade-sphere || pm2 start npm --name "trade-sphere" -- start

  deploy-prod:
    if: github.event.pull_request.merged == true && github.event.pull_request.base.ref == 'main'
    runs-on: ubuntu-latest

    steps:
      - name: Deploy to PROD server
        uses: appleboy/ssh-action@v1.0.3
        with:
          host: ${{ secrets.PROD_SERVER_IP }}
          username: ${{ secrets.PROD_SERVER_USER }}
          key: ${{ secrets.PROD_SSH_KEY }}
          script: |
            cd /root/apps/trade-sphere
            git fetch origin
            git reset --hard origin/main
            npm ci --omit=dev
            pm2 restart trade-sphere || pm2 start npm --name "trade-sphere" -- start
```

---

## 🧩 Deployment Script Explained

```bash
cd /root/apps/trade-sphere
git fetch origin
git reset --hard origin/<branch>
npm ci --omit=dev
pm2 restart trade-sphere || pm2 start npm --name "trade-sphere" -- start
```

### 🔍 Breakdown

* `cd /root/apps/trade-sphere` → navigate to app directory
* `git fetch origin` → get latest code
* `git reset --hard origin/<branch>` → force sync with GitHub
* `npm ci --omit=dev` → install production dependencies
* `pm2 restart ...` → restart app safely

---

## ⚡ PM2 Setup (Required)

Install PM2:

```bash
npm install -g pm2
```

Start app:

```bash
pm2 start npm --name "trade-sphere" -- start
```

Persist after reboot:

```bash
pm2 save
pm2 startup
```

---

## 🧪 Environment Configuration

Each server should have its own `.env` file:

### Dev Server

```env
NODE_ENV=development
PORT=3000
```

### Prod Server

```env
NODE_ENV=production
PORT=3000
```

---

## ⚠️ Important Notes

* Do not edit code directly on the server
* Always deploy via GitHub
* Use `git reset --hard` to avoid conflicts
* Ensure correct permissions for `.ssh`

---

## 🚀 Result

You now have:

* ✅ Automated deployments
* ✅ Separate environments (dev & prod)
* ✅ Zero manual SSH deployment
* ✅ Consistent and reliable releases

---
