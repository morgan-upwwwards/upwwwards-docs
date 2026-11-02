# Logs

Tagged logger used throughout upwwwards: console **log / warn / error**, plus **throw** / **reject**. Messages are prefixed with an author tag (e.g. `[EVENT]: …`).

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import { Logs } from 'upwwwards/scripts'

Logs.log('MY_PLUGIN', 'ready')
Logs.warn('MY_PLUGIN', 'CONFIG MISSING, USING DEFAULTS.')
Logs.error('MY_PLUGIN', err)

if (!el) Logs.throw('MY_PLUGIN', 'ELEMENT NOT FOUND.')
```

| API | Role |
| --- | --- |
| `log` / `warn` / `error` | Console writers; author uppercased in the tag |
| `throw(author, message)` | `throw new Error('[AUTHOR]: …')` |

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

```ts
Logs.mute('MY_PLUGIN')
Logs.unmute('MY_PLUGIN')

Logs.reject(rej, 'MY_PLUGIN', 'FAILED') // rej('[MY_PLUGIN]: …')
```

| API | Role |
| --- | --- |
| `mute` / `unmute` | Per-author silence for console methods only |
| `reject(rej, author, message)` | Call a Promise reject callback with a tagged string |

`throw` / `reject` are **never** muted.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

Values are stringified safely (errors, bigint, circular objects, …).

---

## See also

- [Scripts index](../scripts.md)  
