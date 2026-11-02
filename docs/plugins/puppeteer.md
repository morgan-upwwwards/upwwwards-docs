# Puppeteer

**DOM helpers — not related to Google Puppeteer / headless Chrome.** No browser automation: wait for an element, and drive CSS `transform` via `Matrix4`.

| | |
| --- | --- |
| **Enable** | `plugins: ['Puppeteer']` |
| **Access** | `Up.Puppeteer` |
| **Peers** | none |
| **Scope** | client-only |

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### `wait`

Polls `querySelector` until found or checks exhausted.

```ts
const el = await Up.Puppeteer.wait('.hero')
// scope?: ParentNode-like (default document)
// numberOfChecks?: number (default 10)
// delay?: ms between checks (default 100)
```

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### `Matrix4`

One cached instance per element (`new` again returns the same). Writes batch to `style.transform` (`matrix3d`) on the next Thread frame; call `flush()` to write immediately.

```ts
const el = await Up.Puppeteer.wait('.hero')

if (el instanceof HTMLElement) {
  const matrix = new Up.Puppeteer.Matrix4(el)

  matrix.x = 120
  matrix.rotateZ(Math.PI / 4)
  matrix.scaleX = 1.5
  matrix.flush() // optional immediate write
}
```

| API | Meaning |
| --- | --- |
| `x` / `y` / `z` | Translation (px) |
| `scaleX` / `scaleY` / `scaleZ` | Scale |
| `rotateX` / `rotateY` / `rotateZ(a)` | Add rotation (radians) |
| `setEuler` / `getEulerX|Y|Z` / `resetRotate*` | Absolute / read / clear rotation |
| `skewXY` / `skewXZ` / `skewYX` / `skewYZ` / `skewZX` / `skewZY` | Skew angles (radians) |
| `flush()` | Write `style.transform` now |
| `destroy()` | Drop the cached instance |

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- Constructor reads `getComputedStyle` and takes ownership of `style.transform`.  
- Decomposition follows CSS / `DOMMatrix` conventions (QR: upper skews recovered; lower skews fold into rotation).  
- Call `destroy()` to release the cache entry when you are done with that element.

---

## See also

- [Plugins index](../plugins.md)  
- [Core — Thread](../core.md)  
