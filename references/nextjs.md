# Next.js Integration Reference

Covers both App Router (`app/`) and Pages Router (`pages/`).

---

## Google Analytics 4

### App Router

```bash
npm install @next/third-parties
```

In `app/layout.tsx`:
```tsx
import { GoogleAnalytics } from '@next/third-parties/google'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>{children}</body>
      <GoogleAnalytics gaId={process.env.NEXT_PUBLIC_GA_ID!} />
    </html>
  )
}
```

### Pages Router

In `pages/_app.tsx`:
```tsx
import { GoogleAnalytics } from '@next/third-parties/google'

export default function App({ Component, pageProps }) {
  return (
    <>
      <Component {...pageProps} />
      <GoogleAnalytics gaId={process.env.NEXT_PUBLIC_GA_ID!} />
    </>
  )
}
```

---

## Google Search Console

No package needed. Add the verification string to metadata.

### App Router

In `app/layout.tsx`, add to the exported `metadata` object:
```tsx
export const metadata: Metadata = {
  // ...existing metadata
  verification: {
    google: 'PASTE_VERIFICATION_CONTENT_HERE',
  },
}
```

### Pages Router

In `pages/_document.tsx` (create if missing):
```tsx
import { Html, Head, Main, NextScript } from 'next/document'

export default function Document() {
  return (
    <Html>
      <Head>
        <meta name="google-site-verification" content="PASTE_VERIFICATION_CONTENT_HERE" />
      </Head>
      <body>
        <Main />
        <NextScript />
      </body>
    </Html>
  )
}
```

---

## Microsoft Clarity

Create a client component to avoid inline script injection:

`components/clarity-script.tsx`:
```tsx
'use client'
import { useEffect } from 'react'

export function ClarityScript({ projectId }: { projectId: string }) {
  useEffect(() => {
    if (!projectId) return
    const script = document.createElement('script')
    script.async = true
    script.src = `https://www.clarity.ms/tag/${projectId}`
    document.head.appendChild(script)
  }, [projectId])
  return null
}
```

### App Router — add to `app/layout.tsx`:
```tsx
import { ClarityScript } from '@/components/clarity-script'

export default function RootLayout({ children }: { children: React.ReactNode }) {
  return (
    <html lang="en">
      <body>
        {children}
        <ClarityScript projectId={process.env.NEXT_PUBLIC_CLARITY_ID!} />
      </body>
    </html>
  )
}
```

### Pages Router — add to `pages/_app.tsx`:
```tsx
import { ClarityScript } from '@/components/clarity-script'

export default function App({ Component, pageProps }) {
  return (
    <>
      <Component {...pageProps} />
      <ClarityScript projectId={process.env.NEXT_PUBLIC_CLARITY_ID!} />
    </>
  )
}
```

---

## Sentry

Use the wizard — it handles all config files, source maps, and Vercel integration:

```bash
npx @sentry/wizard@latest -i nextjs
```

The wizard creates `sentry.client.config.ts`, `sentry.server.config.ts`, `sentry.edge.config.ts`, and updates `next.config.js` with `withSentryConfig`. Provide the DSN, org slug, and project slug when prompted.

**Manual setup** (if wizard isn't available):

```bash
npm install @sentry/nextjs
```

`sentry.client.config.ts`:
```ts
import * as Sentry from '@sentry/nextjs'

Sentry.init({
  dsn: process.env.NEXT_PUBLIC_SENTRY_DSN,
  tracesSampleRate: 1.0,
  debug: false,
})
```

Create identical `sentry.server.config.ts` and `sentry.edge.config.ts` with the same content.

In `next.config.js`/`next.config.ts`:
```js
const { withSentryConfig } = require('@sentry/nextjs')

const nextConfig = { /* existing config */ }

module.exports = withSentryConfig(nextConfig, {
  org: process.env.SENTRY_ORG,
  project: process.env.SENTRY_PROJECT,
  authToken: process.env.SENTRY_AUTH_TOKEN,
  silent: true,
  widenClientFileUpload: true,
})
```

**Also set up the Vercel integration**: vercel.com/integrations → search Sentry → install and link project. This enables automatic release tracking on deploy.
