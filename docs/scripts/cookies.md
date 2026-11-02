# Cookies

Read / write / delete cookies via **`document.cookie`**. Defaults: `Secure`, `path=/`, `SameSite=Strict`, 7-day expiry.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

**Browser-only** — throws under SSR. On the server, use **`Up.Adapter`** cookie helpers with a [context](../contexts.md).

```ts
import { Cookies } from 'upwwwards/scripts'

Cookies.set('theme', 'dark', { expiryAfter: 30, sameSite: 'lax' })
Cookies.get('theme')     // 'dark'
Cookies.destroy('theme') // use the same path as set
```

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

| Option | Notes |
| --- | --- |
| `path` | Default `/` |
| `secure` | Default `true` |
| `sameSite` | `'strict' \| 'lax' \| 'none'` (default `'strict'`) |
| `expires` | Absolute `Date` (wins over `expiryAfter`) |
| `expiryAfter` | Days from now (default `7` when omitted on the client helper) |
| `httpOnly` | Ignored by `Cookies` (browser); used by Adapter on SSR |

Also: [adapters — Cookie options](../adapters.md#cookie-options).

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- `Cookies.DEFAULT_CONFIG` exposes the defaults.  
- `getExpirationDate(days)` builds an absolute `Date`.

---

## See also

- [Scripts index](../scripts.md)  
- [Adapters](../adapters.md) · [Contexts](../contexts.md)  
