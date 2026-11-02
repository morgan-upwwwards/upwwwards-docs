# Colors

Color conversion helpers (hex ↔ RGB(A) ↔ HSL(A)), plus related **literal types** (`Colors.RGB`, `ShortHex`, `LongHex`, `HSL`, `HSLA`, …).

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import { Colors } from 'upwwwards/scripts'

Colors.hexToRgb('#f00')           // [255, 0, 0]
Colors.hexToRgbaString('#f008')   // rgba string with alpha
Colors.hexToHSL('#00ff00')
```

| API | Role |
| --- | --- |
| `hexToRgb(hex)` | Parse `#rgb` / `#rrggbb` / `#rrggbbaa` → tuple |
| `hexToRgbaString(hex)` | CSS `rgba(…)` string |
| `hexToHSL(hex)` | `[h, s, l]` or `[h, s, l, a]` |

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

| API | Role |
| --- | --- |
| `normalizeRgb(rgb, fallback?)` | `0–255` → `0–1` channels |
| `getLuminance(rgbNormalized)` | BT.601 luma on 0–1 RGB (`0.299r + 0.587g + 0.114b`) |
| `parseShortHexToLong(hex)` | `#abc` → `aabbcc` digits |
| `DEFAULT_NORMALIZED_RGB` | `[0, 0, 0]` |

```ts
Colors.normalizeRgb([255, 0, 0])  // [1, 0, 0]
Colors.getLuminance([1, 0, 0])
```

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- Invalid inputs may yield `NaN` components (typed via `Maths.NAN` where relevant).  
- Types: `Colors.RGB`, `HSL` / `HSLA`, `ShortHex` / `LongHex` / `AlphaHex`, `HexToRgb` — see the `Colors` namespace in source.

---

## See also

- [Scripts index](../scripts.md)  
- [Maths](maths.md)  
