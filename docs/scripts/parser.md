# Parser

Value **introspection** and object helpers: runtime type tags (`Parser.is`), clone / sameness checks, plus a large TypeScript toolbox (`Merge`, `XOR`, ordered maps, …).

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import { Parser } from 'upwwwards/scripts'

Parser.is(value) // 'STRING' | 'ARRAY' | 'MAP' | …

const copy = Parser.clone({ a: 1, nested: { b: 2 } })
Parser.copy(target, source, (key) => key !== 'skip')
```

### Runtime (static)

| API | Role |
| --- | --- |
| `is(target)` | Coarse runtime type tag |
| `isSame(a, b)` | Deep-ish sameness check |
| `clone(data)` | Structured clone (cycle-aware) |
| `copy(target, source, filter?)` | Shallow assign with optional key filter |
| `reduceWhiteSpace(s)` | Trim + collapse whitespace |

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### `parse`

| API | Role |
| --- | --- |
| `parse(data, parser)` | Walk string / array / object with a sync or async runner |

### Types

`Parser.Merge`, `XOR`, ordered maps, and related mapped types. Prefer JSDoc on the `Parser` namespace when extending configs or plugins.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Used inside upwwwards for config merging and typed maps — `Parser.Merge` / `XOR` power plugin configs.

---

## See also

- [Scripts index](../scripts.md)
