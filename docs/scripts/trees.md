# Trees

Entry point for tree structures. Currently exposes **`Trees.Interval`**: an interval tree storing values on numeric ranges `[start, end]` with overlap queries.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import Trees from 'upwwwards/scripts/Trees'
// or: import { Trees } from 'upwwwards/scripts'

const tree = new Trees.Interval<string>()

tree.add('intro', 0, 100)
tree.get(50, 60) // values overlapping [50, 60]
tree.remove('intro')
tree.clear()
```

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

| Method | Role |
| --- | --- |
| `add(data, start, end)` | Insert `data` on `[start, end]` |
| `remove(data)` | Remove by value identity |
| `get(start, end)` | All values overlapping the query range |
| `getFirstFound(start, end)` | Early-exit overlap query |
| `getSize()` | Number of stored intervals |
| `clear()` | Drop all |

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

Useful for timeline / scroll-range bookkeeping (also used internally by Anime `Timeline` tracks).

---

## See also

- [Scripts index](../scripts.md)  
- [Anime — Timeline](../plugins/anime.md#helper-timeline) (under Going further)  
