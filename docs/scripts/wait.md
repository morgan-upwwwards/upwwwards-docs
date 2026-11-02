# Wait

Timing and readiness helpers: **rAF coalescing**, **debounce** / **throttle**, cancel / pending checks, **sleep**, frame ticks, and **static file** readiness.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import { Wait } from 'upwwwards/scripts'

const onScroll = Wait.throttle(() => { /* … */ }, 100)
Wait.cancel(onScroll)

await Wait.tick(1)
```

| Area | Notes |
| --- | --- |
| `frame` / `debounce` / `throttle` | Wrappers; use `cancel` / `isPending` on identified functions |
| `tick` / sleep-style waits | Client (`requestAnimationFrame`) |

**Client-only** for `frame`, `tick`, and `staticFiles`.

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### `staticFiles`

Wait for fonts / img / video / iframe:

```ts
await Wait.staticFiles({
  eagerOnly: true,
  viewportOnly: true,
  emptySizeOnly: true
})
```

| Key | Default (standalone call) | Role |
| --- | --- | --- |
| `eagerOnly` | `false` unless set | Ignore `img[loading=lazy]` |
| `viewportOnly` | `false` unless set | Skip elements not in viewport (`Is.visible`); always applied to lazy images |
| `emptySizeOnly` | `false` unless set | Only await elements with zero layout size |
| `binaryMask` | all kinds | Bitmask of file kinds (see flags) |
| `delay` | `3000` | Per-element timeout ms; on timeout → warn + treat as ready |
| `limiter` | `document.body` | Root for `img` / `video` / `iframe` (fonts are document-wide) |

**Flags** (OR into `binaryMask`):

| Getter | Kind |
| --- | --- |
| `Wait.STATIC_FILES_FONT_FLAG` | fonts |
| `Wait.STATIC_FILES_IMAGE_FLAG` | `img` |
| `Wait.STATIC_FILES_VIDEO_FLAG` | `video` |
| `Wait.STATIC_FILES_IFRAME_FLAG` | `iframe` |
| `Wait.STATIC_FILES_FULL_BIT_MASK` | all |

```ts
await Wait.staticFiles({
  binaryMask: Wait.STATIC_FILES_FONT_FLAG | Wait.STATIC_FILES_IMAGE_FLAG,
  eagerOnly: true,
  delay: 5000
})
```

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

`Up.Event` waits on `Wait.staticFiles` before each `LOAD`, with defaults **`eagerOnly` / `viewportOnly` / `emptySizeOnly` = `true`**, and **`limiter` forced to `Adapter.root`**. Override via `up.event.staticFilesConfig` — [initializers](../initializers.md#upevent).

---

## See also

- [Scripts index](../scripts.md)  
- [Is](is.md) — `visible` when `viewportOnly`  
- [Core](../core.md) · [Recipes — fonts](../recipes.md#delay-load-until-fonts-or-any-promise)  
