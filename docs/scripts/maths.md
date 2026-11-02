# Maths

Math helpers and related **type-level** utilities (π constants, parse int literals, comparisons, …).

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import { Maths } from 'upwwwards/scripts'

Maths.range(1.4, 0, 1) // 1
Maths.lerp(0, 100, 0.25) // 25
Maths.toRad(180) // ≈ π
```

### Runtime (static)

| API | Role |
| --- | --- |
| `toRad(n)` / `toDeg(n)` | Convert angles |
| `range(n, min?, max?)` | Clamp (default `[0, 1]`) |
| `modulo(n, min?, max?)` | Wrap into range |
| `lerp(p1, p2, progress)` | Linear interpolate |
| `closerTo(n, a, b)` | Whichever of `a`/`b` is nearer to `n` |
| `random(n?)` | Integer in `[0, n)` (default `n = 2`) |
| `toNumber(s)` | Parse numeric string |
| `sum` / `sub` / `mul` | Typed arithmetic helpers |

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### Constants

| API | Role |
| --- | --- |
| `PI_1D4` … `PI_8D4` | π/4 … 2π getters |
| `TO_RAD` / `TO_DEG` | Degree ↔ radian factors |

### Types (namespace)

Examples: `Maths.ParseInt<'007.50'>` → `7.5`, `Maths.PlusOne` / `MinusOne`, `Maths.PI_4D4`.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

- `Maths.NAN` is a number type that may be `NaN` at runtime (used by [Colors](colors.md) for invalid inputs).
- The full type toolbox is documented in JSDoc on the `Maths` namespace.

---

## See also

- [Scripts index](../scripts.md)
- [Colors](colors.md) — uses Maths for channels
