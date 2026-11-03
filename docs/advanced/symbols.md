# Symbols

> **Entirely optional.** This page is *Under the hood* material: internal **Symbol** keys for debugging, patches, or authoring plugins. Everyday app code should use `Up.set`, `Up.Event.sub`, and public plugin APIs instead.

```ts
import { construct, set, destroy, STATUS, apply } from 'upwwwards/symbols'
```

Most symbols are marked `@internal` in source.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

You only need this page if you are:

- debugging lifecycle order, or  
- writing a plugin that shares the same protocol as built-ins, or  
- carefully patching internals.

Otherwise, skip it.

Public lifecycle surface: [core — Under the hood](../core.md#under-the-hood).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional catalog.

### Lifecycle

| Symbol | Role |
| --- | --- |
| `construct` | Once at startup (SSR + client): `Up[construct]`, plugin `[construct]` |
| `set` | Browser: `Up.set` → adapter / App / Thread / Event / plugin `[set]` |
| `destroy` | Browser: `Up.destroy` teardown |
| `load` | Lazy plugins: first-use dependency load |
| `hydrate` | Reserved **unused** symbol (startup uses `construct`). Unrelated to SSR/layout “hydration” — for Screen format cookie / SSR see [Screen](../plugins/screen.md) |

### Runtime state

| Symbol | Role |
| --- | --- |
| `INHERITANCE` | Shared HRT stack `[Adapter, App, Thread, Event]` on the `Hrt` instance and each core module (typed view; each module hydrates its predecessor, not itself) |
| `STATUS` | Thread run state; Nuxt adapter nav cycle; lazy plugin load state |
| `SIGN` | Lazy plugin identity |
| `QUEUE` / `CACHE` / `WAIT` | Lazy plugin pending callbacks / guards / load promise |
| `BUCKET` | Lazy plugin storage for loaded modules / instances |

### Thread / plugins

| Symbol | Role |
| --- | --- |
| `apply` | Hand the Thread loop to an external ticker (e.g. GSAP) |
| `start` / `stop` | Resume / pause (Thread; visibility) |
| `add` / `remove` / `update` / `get` | Internal register / update helpers (e.g. Synthra) |

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- Do not re-call `construct` / `set` from app code; the Vite virtual module and `Up.set` already do.  
- Patching: import the symbol and override carefully — you own the risk under the [LICENSE](license.md).  
- Prefer documenting public behavior in [core](../core.md) / [plugins](../plugins.md) over relying on symbols.

---

## See also

- [Core](../core.md)  
- [Initializers](../initializers.md)  
- [Plugins](../plugins.md)  
