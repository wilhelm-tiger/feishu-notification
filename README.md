<div align="center">

# Feishu Notification

**A GitHub Action that sends [Feishu (Lark)](https://www.feishu.cn) interactive card notifications for pull request events.**

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Feishu%20Notification-blue?logo=github&style=for-the-badge)](https://github.com/marketplace/actions/feishu-notification)


[![Auto Release](https://img.shields.io/github/actions/workflow/status/Waybox-AI/feishu-notification/release.yml?branch=main&style=for-the-badge&logo=github&logoColor=white&label=Auto%20Release)](https://github.com/Waybox-AI/feishu-notification/actions/workflows/release.yml)
[![Latest Release](https://img.shields.io/github/v/release/Waybox-AI/feishu-notification?logo=github&color=blue&style=for-the-badge)](https://github.com/Waybox-AI/feishu-notification/releases/latest)
[![License](https://img.shields.io/github/license/Waybox-AI/feishu-notification?&style=for-the-badge)](LICENSE)
[![Ask DeepWiki](https://deepwiki.com/badge.svg)](https://deepwiki.com/Waybox-AI/feishu-notification)

Built and maintained by [Waybox-AI](https://github.com/Waybox-AI) · Open for anyone to use.

</div>

---

## Notification events

| Event               | Card colour | When it fires                         |
|---------------------|-------------|---------------------------------------|
| PR opened           | Blue        | A pull request is created             |
| New commits pushed  | Orange      | Commits are pushed to an existing PR  |
| PR merged to `main` | Green       | A PR is merged into the `main` branch |

PRs closed without merging, or merged into non-`main` branches, are silently skipped.

---

## Quick start

**1. Add the workflow to your repository**

Create `.github/workflows/ci-cd.yml`:

```yaml
name: Feishu Notifications

on:
  pull_request:
    types: [opened, synchronize, closed]

jobs:
  notify:
    runs-on: ubuntu-latest
    steps:
      - uses: Waybox-AI/feishu-notification@main
        with:
          webhook-url: ${{ secrets.FEISHU_WEBHOOK_URL }}
```

**2. Add the webhook secret**

In your repository go to **Settings → Secrets and variables → Actions → New repository secret**:

- Name: `FEISHU_WEBHOOK_URL`
- Value: the webhook URL from your Feishu bot (see below)

---

## Getting a Feishu webhook URL

1. Open your Feishu group chat
2. Click the **...** menu → **Settings** → **Bots** → **Add Bot** → **Custom Bot**
3. Give the bot a name (e.g. *GitHub Notifier*) and copy the **Webhook URL**
4. Paste it as the `FEISHU_WEBHOOK_URL` secret

---

## Inputs

| Input         | Required | Description                                            |
|---------------|----------|--------------------------------------------------------|
| `webhook-url` | Yes      | Feishu incoming webhook URL. Always pass via a secret. |

---

## Requirements

The action uses `jq` and `curl`, both of which are pre-installed on all GitHub-hosted `ubuntu-latest` runners. No additional setup is needed.
