# Core

After the Vite plugin has built the runtime, you import a single default.

Setup: [getting-started](getting-started.md) · Mental model: [concepts](concepts.md) · Patterns: [recipes](recipes.md).

```ts
import Up from 'upwwwards'
```

| Module | Access | Role |
| --- | --- | --- |
| Adapter | `Up.Adapter` | Bridge to Nuxt / SvelteKit (navigation, refs, cookies, `tick`) — [adapters](adapters.md) |
| App | `Up.App` | Viewport / scroll metrics, app cache, LOAD wait queue |
| Thread | `Up.Thread` | `requestAnimationFrame` scheduler with per-frame budgets |
| Event | `Up.Event` | Page lifecycle + DOM / frame events |

Plugins you enable in the initializer show up as extra getters (`Up.Scroll`, `Up.Anime`, …).

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

What you need to build pages with upwwwards.

### Page cues — `Up.Event`

**Glossary:** `DEATH` = leave · `LIFE` = enter · `LOAD` = ready.

On a **successful** client navigation:

```
Adapter ON_NAVIGATE   →  DEATH  (leave current page, sync)
Adapter AFTER_NAVIGATE →  LIFE  (enter new page)
                       →  LOAD  (after framework tick + readiness)
```

**First load / hard refresh:** `AFTER_NAVIGATE` → `LIFE` → `LOAD` only (no `DEATH`).

| Event | When | Typical use |
| --- | --- | --- |
| `DEATH` | Navigation committed (`ON_NAVIGATE`) | Tear down listeners, animations, Thread tasks |
| `LIFE` | After `AFTER_NAVIGATE` | Construct page features |
| `LOAD` | After `LIFE`, static-file readiness, and `App.wait` | Work that needs the page fully ready |

Cancelled navigations that never reach `ON_NAVIGATE` do **not** fire `DEATH`.  
If a committed nav later fails, listen to Adapter **`ABORTED`** — `LIFE` / `LOAD` will not run for that attempt.

```ts
import Up from 'upwwwards'

function onDeath() { /* tear down */ }
function onLife()  { /* set up */ }

Up.Event.sub([
  ['DEATH', onDeath],
  ['LIFE', onLife],
  ['LOAD', () => {}]
])

// later — same function references
Up.Event.unsub([
  ['DEATH', onDeath],
  ['LIFE', onLife]
])
```

Optional third field `{ once: true }` — the handler runs **once** for that key, then unsubscribes itself (still `unsub` with the same function if you tear down early):

```ts
Up.Event.sub(['LOAD', () => console.log('ready'), { once: true }])
```

`LIFE` / `LOAD` subscribers receive a **Promise** that resolves when every subscriber for that phase has finished. `DEATH` runs synchronously and takes no args.

Always **unsub** with the same function references when the component goes away (including handlers registered with `once`). Event listeners are **client-only**.

### Wire the host — `Up.set` and `Up.Adapter`

Call **`Up.set(…)` once** in the root layout (browser). Later calls are no-ops. Payload depends on the host — [getting-started](getting-started.md).

From app code you typically use:

- `Up.Adapter.sub` / `unsub` — navigation phases (`BEFORE` / `ON` / `AFTER` / `ABORTED`)  
- Cookie helpers — browser or SSR via [contexts](contexts.md)  

Full Adapter API: [adapters.md](adapters.md).

### Metrics, cache, wait — `Up.App`

**Metrics** (browser), filled on `Up.set` and kept fresh on resize / scroll frames:

| Getter | Meaning |
| --- | --- |
| `w` / `h` | Layout viewport |
| `y` / `dY` | Scroll Y / delta |
| `downwards` | Scroll direction |

```ts
Up.Event.sub(['resize', () => {
  console.log(Up.App.w, Up.App.h)
}])
```

**Cache** — store values for the current page / request:

```ts
Up.App.add('heroEl', el)
const el = Up.App.get('heroEl')
Up.App.remove('heroEl')
```

**Delay `LOAD`** until promises settle (register during `LIFE`):

```ts
Up.App.addWait(document.fonts.ready)
```

`LOAD` also waits on static-file readiness after `LIFE`. `addWait` is client-only: on the server the first call logs a warning and later calls are no-ops.

### Minimal page pattern

**Nuxt / Vue**

```ts
import { onMounted, onUnmounted } from 'vue'
import Up from 'upwwwards'

function eDeath() { /* teardown */ }
function eLife()  { /* setup */ }

onMounted(() => {
  Up.Event.sub([['DEATH', eDeath], ['LIFE', eLife]])
})

onUnmounted(() => {
  Up.Event.unsub([['DEATH', eDeath], ['LIFE', eLife]])
})
```

**SvelteKit**

```ts
import Up from 'upwwwards'
import { onMount, onDestroy } from 'svelte'

function eDeath() { /* teardown */ }
function eLife()  { /* setup */ }

onMount(() => {
  Up.Event.sub([['DEATH', eDeath], ['LIFE', eLife]])
})

onDestroy(() => {
  Up.Event.unsub([['DEATH', eDeath], ['LIFE', eLife]])
})
```

More patterns: [recipes](recipes.md).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional. Skip this if Essentials covers your needs.

### More Event keys

| Key | Notes |
| --- | --- |
| `TICK` | Every frame; arg = `Thread.timestamp` (ms) — prefer this for simple per-frame work |
| `EARTHQUAKE` | Root resize (`ResizeObserver` on `Adapter.root`), debounced ~50 ms — name = “layout shook”, not a device API |
| `resize` | `window` resize, debounced ~50 ms |
| `scroll` | `window` scroll |
| `visibilitychange` | Pauses/resumes Thread; arg = `document.hidden` |

Declare custom events in `up.event.enhance` — [initializers](initializers.md#upevent).

### Custom DOM target

Bind a listener on a specific node:

```ts
const onClick = (e: MouseEvent) => console.log(e.clientX)

Up.Event.sub(['click', onClick, Up.Event.PASSIVE], document.body)
Up.Event.unsub(['click', onClick], document.body)
```

`Up.Event.PASSIVE` / `IMPASSIVE` are ready-made **DOM** options objects (`AddEventListenerOptions`). They are not the Event-manager `{ once: true }` ticket option — that third field only applies when you omit the DOM `subscriber`.

### Extra App metrics and cache notes

| Getter | Meaning |
| --- | --- |
| `cw` / `ch` | Client size |
| `sw` / `sh` | Screen |
| `lw` / `lh` | “Large” / visual viewport helpers |
| `y` / `dY` / `downwards` | Scroll position / delta / direction |
| `AGENT` / `NAVIGATOR` | Coarse UA family |

- Metrics refresh via Thread modifiers on resize (`8`) / scroll (`32`) when those flags are armed (`Thread.sub` / `Thread.arm`). Public helpers: `App.updateScreenVars()` / `App.updateScrollVars()` (prefer the modifier path).  
- Augment cache types in `customizable/types/config.d.ts` (`AppCache`) — [example](initializers.md#generated-types-augmentations).  
- Keys starting with `$` are meant for adapter refs (reactive).  
- On the server, the cache is **request-scoped** (needs [contexts](contexts.md)). Prefer static string keys.

### `Up.Thread` — when `TICK` is not enough

Most apps should prefer **`Up.Event.sub(['TICK', …])`**. Use Thread directly for budgets, priorities, drivers, or spreading work across frames.

Each processed frame runs, in order: **drivers** → **modifiers** → **tasks by ascending flag** (for each flag: **forever** then **flow**). Soft budgets (`up.thread.flowBudget` / `foreverBudget`, defaults ~8 ms / ~4 ms) apply separately to forever and flow.

**Default `Thread.sub` is one-shot (flow)** — not a per-frame loop.

| API | Role |
| --- | --- |
| `Thread.sub(task, flag?, …args)` | Schedule a task (flow unless marked forever); arms modifiers of `flag` |
| `Thread.unsub(task)` | Remove it |
| `Thread.markForever(task)` | Mark **before** `sub` so it runs every frame |
| `Thread.unmarkForever(task)` | Clear forever mark + unsub |
| `Thread.wait(task)` | Promise when that task completes (or is unsub’d) |
| `Thread.addModifier(cb, flag)` | Hook before tasks of a flag, once per frame when armed |
| `Thread.arm(flag)` | Arm modifiers of `flag` for the next modifiers phase (no task) |
| `Thread.addDriver(cb)` | Hook at the **start** of every frame (before modifiers) |
| `Thread.removeDriver(cb)` | Remove a driver |
| `Thread.timestamp` / `delta` | Current frame time / delta |
| `Thread.DEFAULT_FLAG` | `8192` — **default flag** of `Thread.sub` |
| `Thread.MAX_FLAG` | `32768` — highest flag (end-of-frame forever work, e.g. Anime) |

Priority **flags** are powers of 2 from `1` to `32768` (lower runs first). Built-ins include `EARTHQUAKE` `2`, `resize` `8`, `scroll` `32`, Event default `128`, Observer `512`, Synthra `2048`, **default `8192`**, spare `16384`, **max `32768`**. Omitting `flag` uses **`DEFAULT_FLAG` (`8192`)**, not Event’s `128` and not `MAX_FLAG`.

```ts
const spin = () => { /* … */ }

Up.Thread.markForever(spin) // must be before sub
Up.Thread.sub(spin)
// later
Up.Thread.unsub(spin)
```

**Generators** — spread work across frames when the flow budget is spent:

```ts
function* build(items: HTMLElement[]) {
  for (const item of items) {
    item.classList.add('ready')
    yield
  }
}

Up.Thread.sub(build, 128, [...document.querySelectorAll<HTMLElement>('.item')])
await Up.Thread.wait(build)
```

Budgets: [initializers — `up.thread`](initializers.md#upthread).

### Event subtleties

- Subscribers of a given key run in **reverse subscription order**. Duplicate `(key, callback)` pairs are ignored.  
- **`{ once: true }`:** runs once for that key, then auto-unsubscribes. Unrelated to the bullet below.  
- **Immediate one-shot (not stored):** if you `sub` an unknown / unregistered key, or `LOAD` after the current `LOAD` phase has already started, the callback runs **immediately** with no args and is **not** kept.  
- `LIFE` / `LOAD` Promise args are useful for chaining; you can ignore them.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional. For debugging, patching, or understanding boot — not required for app features.

### Runtime boot

1. **Construct** — the Vite virtual module runs `Up[construct]` (server + client) with your adapter and plugins.  
2. **`Up.set(adapterConfig)`** — once in the browser; wires adapter, App, Thread, Event, plugin `[set]`.  
3. **`Up.destroy()`** *(optional)* — client-only full teardown (plugins → Event → Thread → App). **Irreversible:** you cannot call `Up.set` again afterwards. Most apps never call it; avoid in HMR / per-test cleanup.

Low-level symbol keys: [advanced/symbols](advanced/symbols.md).

### `Up.patch(PluginClass)`

Replace a registered plugin with a **direct subclass** before `Up.set` (subclass `[construct]` is **not** re-run):

```ts
import Scroll from 'upwwwards/plugins/Scroll'

class MyScroll extends Scroll {}

Up.patch(MyScroll)
Up.set(adapterConfig)
```

Deep imports exist for Transmitter / Scroll / Anime / Puppeteer / Synthra — not for Observer / Screen (barrel `upwwwards/plugins` only). See [compatibility — exports](compatibility.md#package-exports).

### Adapter extras

`getRef` / `setRef` / `isRef`, `tick()`, `root`, `BROWSER` — [adapters](adapters.md#going-further).

---

## See also

- [Concepts](concepts.md) · [FAQ](faq.md) · [Recipes](recipes.md)  
- [Getting started](getting-started.md) · [Documentation index](index.md)  
- [Adapters](adapters.md) · [Initializers](initializers.md) · [Symbols](advanced/symbols.md)  
