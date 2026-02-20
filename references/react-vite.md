# React/Vite Integration Reference

---

## Google Analytics 4

```bash
npm install react-ga4
```

In `src/main.tsx` (or `src/App.tsx`):
```tsx
import ReactGA from 'react-ga4'

ReactGA.initialize(import.meta.env.VITE_GA_ID)
```

For route tracking with React Router, call `ReactGA.send('pageview')` on route changes:
```tsx
import { useEffect } from 'react'
import { useLocation } from 'react-router-dom'
import ReactGA from 'react-ga4'

export function usePageTracking() {
  const location = useLocation()
  useEffect(() => {
    ReactGA.send({ hitType: 'pageview', page: location.pathname })
  }, [location])
}
```

Call `usePageTracking()` inside your router wrapper component.

> Vite uses `VITE_` prefix. All public env vars: `VITE_GA_ID`, `VITE_CLARITY_ID`, etc.

---

## Google Search Console

Add the meta tag to `index.html`:
```html
<head>
  <meta name="google-site-verification" content="PASTE_VERIFICATION_CONTENT_HERE" />
</head>
```

---

## Microsoft Clarity

Create `src/components/ClarityScript.tsx`:
```tsx
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

In `src/App.tsx`:
```tsx
import { ClarityScript } from './components/ClarityScript'

function App() {
  return (
    <>
      <ClarityScript projectId={import.meta.env.VITE_CLARITY_ID} />
      {/* rest of app */}
    </>
  )
}
```

---

## Sentry

```bash
npm install @sentry/react
```

In `src/main.tsx`, before rendering:
```tsx
import * as Sentry from '@sentry/react'

Sentry.init({
  dsn: import.meta.env.VITE_SENTRY_DSN,
  integrations: [
    Sentry.browserTracingIntegration(),
    Sentry.replayIntegration(),
  ],
  tracesSampleRate: 1.0,
  replaysSessionSampleRate: 0.1,
  replaysOnErrorSampleRate: 1.0,
})
```

For source map uploads, first install the plugin:

```bash
npm install @sentry/vite-plugin --save-dev
```

Then in `vite.config.ts`:
```ts
import { defineConfig } from 'vite'
import react from '@vitejs/plugin-react'
import { sentryVitePlugin } from '@sentry/vite-plugin'

export default defineConfig({
  plugins: [
    react(),
    sentryVitePlugin({
      org: process.env.SENTRY_ORG,
      project: process.env.SENTRY_PROJECT,
      authToken: process.env.SENTRY_AUTH_TOKEN,
    }),
  ],
  build: {
    sourcemap: true,
  },
})
```

---

## Vite Environment Variable Notes

Vite uses `VITE_` prefix for client-exposed vars, accessed via `import.meta.env.VITE_FOO`.

In `.env.local`:
```
VITE_GA_ID=G-XXXXXXXXXX
VITE_CLARITY_ID=xxxxxxxxxx
VITE_SENTRY_DSN=https://...@sentry.io/...
SENTRY_ORG=your-org
SENTRY_PROJECT=your-project
SENTRY_AUTH_TOKEN=sntrys_...
```

Update Vercel env var names to match (`VITE_GA_ID` not `NEXT_PUBLIC_GA_ID`).
