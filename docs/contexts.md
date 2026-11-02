# Contexts

SSR **request context** so Adapter cookie helpers (and request-scoped stores such as `Up.App` cache / `Up.Screen`) work on the server.

Browser-only apps can skip this page entirely.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

Import the host module once so it registers the server implementation.

### Nuxt

`server/plugins/upwwwards-context.ts`:

```ts
export { default } from 'upwwwards/contexts/nuxt'
```

Peer: `h3` (usually already present with Nuxt).

### SvelteKit

`src/hooks.server.ts`:

```ts
import { sequence } from '@sveltejs/kit/hooks'
import sveltekitHandle from 'upwwwards/contexts/sveltekit'

export const handle = sequence(sveltekitHandle)
```

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### App usage

```ts
import Up from 'upwwwards'

// Browser: document.cookie — Server: active context required
Up.Adapter.setCookie('theme', 'dark', { expiryAfter: 30 })
Up.Adapter.getCookie('theme')
Up.Adapter.destroyCookie('theme', { path: '/' })
```

Cookie option table: [adapters — Cookie options](adapters.md#cookie-options).

Without a server context, server-side cookie / request-scoped cache calls are no-ops or unsafe.

Do **not** use `Cookies` from `upwwwards/scripts` on the server (`document` is missing); prefer the Adapter.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- **Nuxt** — h3 cookie helpers on the current request event (`getCookie` / `setCookie` / `deleteCookie`).  
- **SvelteKit** — `event.cookies` for the duration of `resolve`.

---

## See also

- [Getting started](getting-started.md)  
- [Adapters](adapters.md)  
- [Scripts — Cookies](scripts/cookies.md)  
- [Plugins — Screen](plugins/screen.md)  
