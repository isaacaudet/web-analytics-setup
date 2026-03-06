# web-analytics-setup

A Claude Code skill that automates the full analytics and monitoring setup for Vercel-deployed web projects. One command instead of an afternoon.

## What it sets up

| Service | What you get |
|---|---|
| **Google Analytics 4** | Page view tracking, event tracking |
| **Google Search Console** | Site verification + sitemap submission |
| **Microsoft Clarity** | Session recordings, heatmaps |
| **Sentry** | Error monitoring, performance tracing |

## Frameworks supported

- Next.js (App Router + Pages Router)
- Astro
- React + Vite

## Install

```bash
npx skills add isaacaudet/web-analytics-setup
```

## Usage

Once installed, just tell Claude:

```
Set up analytics for this project
```

Claude will:
1. Detect your framework automatically
2. Ask for your credentials (GA4 ID, Clarity ID, Sentry DSN, etc.)
3. Install required packages
4. Insert tracking code in the right places
5. Create `.env.local` with all variables
6. Set Vercel environment variables via CLI
7. Verify `.gitignore` is protecting your secrets

## What you need to have ready

| Service | What to grab |
|---|---|
| GA4 | Measurement ID (`G-XXXXXXXXXX`) — Analytics → Admin → Data Streams |
| Search Console | Verification meta tag content value |
| Clarity | Project ID — clarity.microsoft.com → Settings → Overview |
| Sentry | DSN, org slug, project slug, auth token |

You don't need all four — just tell Claude which services you want.

## Why

Setting up analytics on every new project means the same 30-45 minutes of copying IDs, installing packages, adding env vars, and configuring Vercel every single time. This skill does all of it in one shot.
