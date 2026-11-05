# Adapters

The **adapter** connects upwwwards to your host framework. The Vite plugin selects it (`adapter: 'nuxt' | 'svelte'`); you use it as `Up.Adapter`.

```ts
import Up from 'upwwwards'

Up.Adapter.sub([['AFTER_NAVIGATE', () => {}]])
```

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### Navigation phases

Subscribe with `Up.Adapter.sub` / `unsub` — keep the same callback references for unsubscribe.

| Phase | Meaning |
| --- | --- |
| `BEFORE_NAVIGATE` | In-app client navigation about to start |
| `ON_NAVIGATE` | Navigation committed / in progress → Event **DEATH** |
| `AFTER_NAVIGATE` | New page ready → **LIFE** then **LOAD** |
| `ABORTED` | Nav failed after the cycle started — no `AFTER` |

**Success:** `BEFORE` → `ON` → `AFTER`.  
**Failure after start:** `ABORTED` instead of `AFTER`.

**First load / hard refresh:** only `AFTER` (no `BEFORE` / `ON`). Details: [getting-started](getting-started.md#first-load-note).

Event lifecycle: [core — Essentials](core.md#essentials).

### `Up.set` config by host

**Nuxt** — call once in a root layout `<script setup>`. No host APIs to pass; optional `{ rootSelector? }` only.

```ts
Up.set()
// or: Up.set({ rootSelector: '#app' })
```

**SvelteKit** — `{ beforeNavigate, onNavigate, afterNavigate, rootSelector? }`  
(from `$app/navigation`, during component init)

```ts
import { beforeNavigate, onNavigate, afterNavigate } from '$app/navigation'
Up.set({ beforeNavigate, onNavigate, afterNavigate })
```

Full layout copy-paste: [getting-started](getting-started.md).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### Shared API

| API | Role |
| --- | --- |
| `sub` / `unsub` | Navigation phases |
| `getRef` / `setRef` / `isRef` | Framework reactive refs |
| `tick()` | Wait for framework DOM flush |
| `setCookie` / `getCookie` / `destroyCookie` | Cookies (browser or SSR via [contexts](contexts.md)) |
| `root` | App root element (from `rootSelector` or `body`) |
| `BROWSER` | `typeof window !== 'undefined'` |

Refs: Vue `ref` on Nuxt · Svelte `writable` on SvelteKit.  
`tick`: Vue `nextTick` · Svelte `tick`.

### `rootSelector`

Optional CSS selector in `Up.set({ …, rootSelector: '#app' })`. Resolves to `Up.Adapter.root` (fallback: `document.body`).

Used as:

- Target of **`EARTHQUAKE`** (`ResizeObserver` on the root)  
- **`limiter`** for Event’s pre-`LOAD` `Wait.staticFiles` wait  

If you mount the app in a non-`body` shell, set `rootSelector` so resize / static-file readiness match that shell.

### Cookie options

Aligned with [Cookies](scripts/cookies.md); Adapter routes to `document.cookie` or the SSR context.

| Option | Notes |
| --- | --- |
| `path` | Default `/` |
| `secure` | Default `true` (browser defaults) |
| `sameSite` | `'strict' \| 'lax' \| 'none'` (default `'strict'`) |
| `expires` | Absolute `Date` (wins over `expiryAfter`) |
| `expiryAfter` | Days from now. Browser falls back to 7 days if neither expiry is set; **server does not** invent a default expiry |
| `httpOnly` | **Server only** (defaults to `true` on the server when omitted) |

```ts
Up.Adapter.setCookie('theme', 'dark', { expiryAfter: 30, sameSite: 'lax' })
Up.Adapter.getCookie('theme')
Up.Adapter.destroyCookie('theme', { path: '/' })
```

### Host caveats

**Nuxt:** call `Up.set` in a layout/setup context. Route middleware that aborts **before** the adapter has wired navigation never starts the Adapter cycle (no `BEFORE` / `ABORTED`). Guards registered *after* `Up.set` can abort after `BEFORE`/`ON`.

**Nuxt + `pageTransition`:** `AFTER_NAVIGATE` / `LIFE` wait until the new page is ready for work — including when you use page transitions (e.g. `out-in`). No extra app code required.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional. Exact sources of each phase — for debugging navigation.

### Nuxt (`adapter: 'nuxt'`)

| Phase | Source |
| --- | --- |
| `BEFORE` + `ON` | Client path change (wired by the adapter) |
| `AFTER` | New page ready (respects `pageTransition` when configured) |
| `ABORTED` | Failed navigation after the cycle started |

### SvelteKit (`adapter: 'svelte'`)

| Phase | Source |
| --- | --- |
| `BEFORE` | `beforeNavigate` if in-app (`to.route.id` set, `!willUnload`) |
| `ON` | `onNavigate` (page still mounted) → **DEATH** |
| `AFTER` | `afterNavigate` (**including first load**) |
| `ABORTED` | `complete.catch` after a `BEFORE` was emitted |

---

## See also

- [Concepts](concepts.md) · [FAQ](faq.md)  
- [Getting started](getting-started.md) · [Core](core.md)  
- [Contexts](contexts.md) · [Initializers](initializers.md)  
