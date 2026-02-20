# Astro Integration Reference

---

## Google Analytics 4

No package needed. Add the gtag script to your base layout.

In your base layout component (e.g., `src/layouts/Layout.astro`):
```astro
---
// Layout.astro
---
<html>
  <head>
    <!-- existing head content -->
    <script
      is:inline
      async
      src={`https://www.googletagmanager.com/gtag/js?id=${import.meta.env.PUBLIC_GA_ID}`}
    ></script>
    <script is:inline define:vars={{ gaId: import.meta.env.PUBLIC_GA_ID }}>
      window.dataLayer = window.dataLayer || [];
      function gtag() { window.dataLayer.push(arguments); }
      gtag('js', new Date());
      gtag('config', gaId);
    </script>
  </head>
  <body>
    <slot />
  </body>
</html>
```

> Astro uses `PUBLIC_` prefix (not `NEXT_PUBLIC_`). Update env var names accordingly: `PUBLIC_GA_ID`, `PUBLIC_CLARITY_ID`.

---

## Google Search Console

Add the meta tag in the `<head>` of your base layout:
```astro
<head>
  <meta name="google-site-verification" content="PASTE_VERIFICATION_CONTENT_HERE" />
</head>
```

---

## Microsoft Clarity

In your base layout's `<head>`:
```astro
<script
  is:inline
  async
  src={`https://www.clarity.ms/tag/${import.meta.env.PUBLIC_CLARITY_ID}`}
></script>
```

If Clarity requires the full initialization snippet (queue setup), use a separate `.ts` file loaded via a script tag:

`src/scripts/clarity.ts`:
```ts
const clarityId = import.meta.env.PUBLIC_CLARITY_ID
if (clarityId) {
  const script = document.createElement('script')
  script.async = true
  script.src = `https://www.clarity.ms/tag/${clarityId}`
  document.head.appendChild(script)
}
```

In layout:
```astro
<script src="../scripts/clarity.ts"></script>
```

---

## Sentry

```bash
npx astro add @sentry/astro
```

This adds `@sentry/astro` to `package.json` and prompts for configuration.

If manual setup is needed:

```bash
npm install @sentry/astro
```

In `astro.config.mjs`:
```js
import { defineConfig } from 'astro/config'
import sentry from '@sentry/astro'

export default defineConfig({
  integrations: [
    sentry({
      dsn: process.env.SENTRY_DSN,
      sourceMapsUploadOptions: {
        project: process.env.SENTRY_PROJECT,
        authToken: process.env.SENTRY_AUTH_TOKEN,
      },
    }),
  ],
})
```

> Use `process.env` here, not `import.meta.env` — `astro.config.mjs` runs in Node.js build context where `import.meta.env` is not fully populated.

`sentry.client.config.ts`:
```ts
import * as Sentry from '@sentry/astro'

Sentry.init({
  dsn: import.meta.env.PUBLIC_SENTRY_DSN,
  tracesSampleRate: 1.0,
})
```

---

## Astro Environment Variable Notes

Astro uses Vite's env system:
- Client-side: `PUBLIC_` prefix, accessed via `import.meta.env.PUBLIC_FOO`
- Server-side only: no prefix, accessed via `import.meta.env.FOO`

In `.env.local`:
```
PUBLIC_GA_ID=G-XXXXXXXXXX
PUBLIC_CLARITY_ID=xxxxxxxxxx
PUBLIC_SENTRY_DSN=https://...@sentry.io/...
SENTRY_DSN=https://...@sentry.io/...
SENTRY_ORG=your-org
SENTRY_PROJECT=your-project
SENTRY_AUTH_TOKEN=sntrys_...
```

Update Vercel env var names to match (`PUBLIC_GA_ID` not `NEXT_PUBLIC_GA_ID`).
