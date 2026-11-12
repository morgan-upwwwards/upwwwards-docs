# Arrays

**Tuple / array TypeScript utilities** (`Join`, `Pad`, `Includes`, `Push` / `Pop`, …) plus a constructible **`Arrays.CallStack`** for ordered callback stacks.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import { Arrays } from 'upwwwards/scripts'

type Line = Arrays.Join<[1, 'a', true], ', '> // '1, a, true'

const onResize = new Arrays.CallStack<(width: number) => void>()
const log = (width: number) => console.log(width)

onResize.add(log)
onResize.loop(window.innerWidth)
onResize.remove(log)
```

Deep import also available: `upwwwards/scripts/Arrays`.

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

| API | Role |
| --- | --- |
| `new CallStack(...callbacks)` | Optional initial callbacks |
| `add(cb)` / `remove(cb)` | Register / unregister (duplicates allowed on add) |
| `clear()` | Remove every callback (safe during `loop` / `gen`) |
| `has(cb)` | Whether `cb` is registered |
| `loop(...args)` | Call every callback with the args |
| `gen(...args)` | Generator that yields after each callback (used by Thread) |
| `length` | Callback count |

Errors in `loop` / `gen` are logged with [`Logs.error`](logs.md) and stop the rest of that iteration (not rethrown).

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- Safe to add/remove/`clear` during iteration: removals apply immediately without skipping the next callback; additions run from the next iteration; `clear` skips remaining callbacks.  
- Deep tuple helpers live on the `Arrays` namespace in source (`Join`, `Pad`, `Includes`, …).

---

## See also

- [Scripts index](../scripts.md)  
- [Logs](logs.md) · [Core — Thread](../core.md)  
