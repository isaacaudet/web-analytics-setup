---
name: web-analytics-setup
description: Automates adding Google Analytics 4, Google Search Console verification, Microsoft Clarity, and Sentry to web projects deployed on Vercel. Use when the user wants to set up analytics, error monitoring, session recording, or search console verification on a new or existing project. Handles Next.js (App Router and Pages Router), Astro, and React/Vite projects. Installs packages, inserts tracking code, creates .env.local entries, and sets Vercel environment variables via CLI.
---

# Web Analytics Setup

Automate the full setup of GA4, Google Search Console, Microsoft Clarity, and Sentry for Vercel-deployed sites.

## Workflow

### 1. Detect Framework

Read `package.json` dependencies to determine the framework:
- `"next"` present → Next.js (check if `app/` directory exists = App Router, else Pages Router)
- `"astro"` present → Astro
- `"vite"` + `"react"` present → React/Vite

Load the framework reference file before writing any code:
- Next.js → `references/nextjs.md`
- Astro → `references/astro.md`
- React/Vite → `references/react-vite.md`

### 2. Gather Credentials

Tell the user what to collect before starting. Ask for only what's needed for services they want:

| Service | What to get | Where to find it |
|---------|-------------|-----------------|
| GA4 | Measurement ID (`G-XXXXXXXXXX`) | analytics.google.com → Admin → Data Streams → Web stream details |
| Google Search Console | Verification meta tag `content` value | search.google.com/search-console → Add property → HTML tag method |
| Microsoft Clarity | Project ID (short alphanumeric string) | clarity.microsoft.com → project → Settings → Overview |
| Sentry | DSN, org slug, project slug, auth token | sentry.io → project Settings → Client Keys (DSN) + User Settings → Auth Tokens |

Wait for all credentials before proceeding.

### 3. Set Environment Variables

**Create/update `.env.local`** with the gathered values:
```
NEXT_PUBLIC_GA_ID=G-XXXXXXXXXX
NEXT_PUBLIC_CLARITY_ID=xxxxxxxxxx
SENTRY_DSN=https://...@sentry.io/...
NEXT_PUBLIC_SENTRY_DSN=https://...@sentry.io/...
SENTRY_ORG=your-org-slug
SENTRY_PROJECT=your-project-slug
SENTRY_AUTH_TOKEN=sntrys_...
```

**Verify `.gitignore` includes `.env.local`** — add it if missing.

**Set Vercel env vars via CLI** (run each, select all environments when prompted):
```bash
vercel env add NEXT_PUBLIC_GA_ID
vercel env add NEXT_PUBLIC_CLARITY_ID
vercel env add SENTRY_DSN
vercel env add NEXT_PUBLIC_SENTRY_DSN
vercel env add SENTRY_ORG
vercel env add SENTRY_PROJECT
vercel env add SENTRY_AUTH_TOKEN
```

Confirm with `vercel env ls` after adding.

> GSC verification content is embedded directly in code, not stored as an env var.

### 4. Install Packages & Add Code

Follow the framework-specific reference file loaded in step 1.

### 5. Verify Setup

- [ ] `.env.local` has all required vars
- [ ] `.gitignore` includes `.env.local`
- [ ] No hardcoded IDs in source — only `process.env.VAR_NAME` or `import.meta.env.VAR_NAME`
- [ ] `vercel env ls` shows all vars
- [ ] `npm run build` (or framework equivalent) passes locally

### Google Search Console: Manual Step Required

After adding the meta tag, remind the user:
> "The verification tag is in place. Go to search.google.com/search-console, add your property, choose the HTML tag method, and click Verify. Also submit your sitemap at `/sitemap.xml` once verified."
