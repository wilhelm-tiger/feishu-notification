<div align="center">

# Feishu Notification

**A GitHub Action that sends [Feishu (Lark)](https://www.feishu.cn) interactive card notifications for pull request events.**

[![GitHub Marketplace](https://img.shields.io/badge/Marketplace-Feishu%20Notification-blue?logo=github&style=for-the-badge)](https://github.com/marketplace/actions/feishu-notification)

[![Auto Release](https://img.shields.io/github/actions/workflow/status/Waybox-AI/feishu-notification/release.yml?branch=main&style=for-the-badge&logo=github&logoColor=white&label=Auto%20Release)](https://github.com/Waybox-AI/feishu-notification/actions/workflows/release.yml)
[![Latest Release](https://img.shields.io/github/v/release/Waybox-AI/feishu-notification?logo=github&color=blue&style=for-the-badge)](https://github.com/Waybox-AI/feishu-notification/releases/latest)
[![DeepWiki](https://img.shields.io/badge/DeepWiki-Waybox--AI%2Ffeishu--notification-blue.svg?&style=for-the-badge&logo=data:image/png;base64,iVBORw0KGgoAAAANSUhEUgAAACwAAAAyCAYAAAAnWDnqAAAAAXNSR0IArs4c6QAAA05JREFUaEPtmUtyEzEQhtWTQyQLHNak2AB7ZnyXZMEjXMGeK/AIi+QuHrMnbChYY7MIh8g01fJoopFb0uhhEqqcbWTp06/uv1saEDv4O3n3dV60RfP947Mm9/SQc0ICFQgzfc4CYZoTPAswgSJCCUJUnAAoRHOAUOcATwbmVLWdGoH//PB8mnKqScAhsD0kYP3j/Yt5LPQe2KvcXmGvRHcDnpxfL2zOYJ1mFwrryWTz0advv1Ut4CJgf5uhDuDj5eUcAUoahrdY/56ebRWeraTjMt/00Sh3UDtjgHtQNHwcRGOC98BJEAEymycmYcWwOprTgcB6VZ5JK5TAJ+fXGLBm3FDAmn6oPPjR4rKCAoJCal2eAiQp2x0vxTPB3ALO2CRkwmDy5WohzBDwSEFKRwPbknEggCPB/imwrycgxX2NzoMCHhPkDwqYMr9tRcP5qNrMZHkVnOjRMWwLCcr8ohBVb1OMjxLwGCvjTikrsBOiA6fNyCrm8V1rP93iVPpwaE+gO0SsWmPiXB+jikdf6SizrT5qKasx5j8ABbHpFTx+vFXp9EnYQmLx02h1QTTrl6eDqxLnGjporxl3NL3agEvXdT0WmEost648sQOYAeJS9Q7bfUVoMGnjo4AZdUMQku50McDcMWcBPvr0SzbTAFDfvJqwLzgxwATnCgnp4wDl6Aa+Ax283gghmj+vj7feE2KBBRMW3FzOpLOADl0Isb5587h/U4gGvkt5v60Z1VLG8BhYjbzRwyQZemwAd6cCR5/XFWLYZRIMpX39AR0tjaGGiGzLVyhse5C9RKC6ai42ppWPKiBagOvaYk8lO7DajerabOZP46Lby5wKjw1HCRx7p9sVMOWGzb/vA1hwiWc6jm3MvQDTogQkiqIhJV0nBQBTU+3okKCFDy9WwferkHjtxib7t3xIUQtHxnIwtx4mpg26/HfwVNVDb4oI9RHmx5WGelRVlrtiw43zboCLaxv46AZeB3IlTkwouebTr1y2NjSpHz68WNFjHvupy3q8TFn3Hos2IAk4Ju5dCo8B3wP7VPr/FGaKiG+T+v+TQqIrOqMTL1VdWV1DdmcbO8KXBz6esmYWYKPwDL5b5FA1a0hwapHiom0r/cKaoqr+27/XcrS5UwSMbQAAAABJRU5ErkJggg==)](https://deepwiki.com/Waybox-AI/feishu-notification)
[![License](https://img.shields.io/github/license/Waybox-AI/feishu-notification?&style=for-the-badge)](LICENSE)

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
