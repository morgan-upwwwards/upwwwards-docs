# Observer

Shares one **MutationObserver**, one **IntersectionObserver**, and one **ResizeObserver** for the whole app. Native callbacks are batched and dispatched on `Up.Thread` (flag `512`), not inline in the browser observer callback.

| | |
| --- | --- |
| **Enable** | `plugins: ['Observer']` |
| **Access** | `Up.Observer` |
| **Peers** | none |
| **Scope** | client-only |

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### Subscribe and unsubscribe

Call `sub` / `unsub` **after** `Up.set`.

```ts
import Up from 'upwwwards'

function onResize(entry: ResizeObserverEntry) {
  console.log(entry.contentRect)
}

Up.Observer.sub(element, [
  ['RESIZE', onResize],
  ['INTERSECTION', (entry) => console.log(entry.isIntersecting)],
  ['MUTATION', (record) => console.log(record.type), { childList: true }]
])

Up.Observer.unsub(element, [['RESIZE', onResize]])
```

Always **`unsub` with the same callback references** when the element or component goes away (same rule as `Event.sub`).

### Kinds at a glance

| Kind | Callback arg | Options (3rd tuple item) |
| --- | --- | --- |
| `MUTATION` | `MutationRecord` | **Required** `MutationObserverInit` |
| `INTERSECTION` | `IntersectionObserverEntry` | None |
| `RESIZE` | `ResizeObserverEntry` | Optional `ResizeObserverOptions` |

Recipe: [recipes — Observer](../recipes.md#observer-resize).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### Option details

- `MUTATION`: `subtree: true` is **not allowed**.
- `INTERSECTION`: no options — one shared observer with browser defaults (viewport root, threshold `0`).
- `RESIZE`: avoid observing **inline** elements (ResizeObserver does not report them usefully).

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

### Shared observers

- One native observer instance **per kind** for the whole app; callbacks are stored in a `WeakMap` per element.  
- Internal dispatch order per batch: `MUTATION` → `INTERSECTION` → `RESIZE`.  
- `MUTATION` has no `unobserve`: unsubscribing disconnects and re-observes remaining elements from an internal history map.  

---

## See also

- [Plugins index](../plugins.md)  
- [Core — Thread](../core.md)  
