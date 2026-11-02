# Initializers

The **Vite plugin** is the build-time entry that builds the runtime for Nuxt 4 or SvelteKit 2.

```ts
import initializer from 'upwwwards/initializers/vite'
```

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### Minimal setup

```ts
initializer({ adapter: 'nuxt' })   // or 'svelte'
```

Then call **`Up.set(…)` once** in the browser — [getting-started](getting-started.md).

| Option | Required | Description |
| --- | --- | --- |
| `adapter` | yes | `'nuxt'` or `'svelte'` |
| `plugins` | no | Plugin names or `{ name, config }` — [plugins](plugins.md) |
| `classter` | no | Enable Classter — [classter](classter.md) |
| `setupIn` | no | Where to generate `upwwwards/` (defaults: Nuxt `/app`, SvelteKit `/src/lib`) |
| `readIn` | no | Classter scan root (defaults: Nuxt `/app`, SvelteKit `/src`) |
| `up` | no | Core config (`event`, `thread`) — see [Going further](#going-further) |

Duplicate plugin names: warning, first wins.

### Examples

**Nuxt**

```ts
import initializer from 'upwwwards/initializers/vite'

export default defineNuxtConfig({
  vite: {
    plugins: [
      initializer({
        adapter: 'nuxt',
        // plugins: ['Scroll', 'Anime']
      })
    ]
  }
})
```

**SvelteKit**

```ts
import { sveltekit } from '@sveltejs/kit/vite'
import { defineConfig } from 'vite'
import initializer from 'upwwwards/initializers/vite'

export default defineConfig({
  plugins: [
    initializer({ adapter: 'svelte' }),
    sveltekit()
  ]
})
```

The plugin uses `enforce: 'pre'`. After the first Vite run it writes types under `setupIn`.

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### `up.thread`

Soft frame budgets (ms). Must be `> 0`.

| Key | Default | Meaning |
| --- | --- | --- |
| `flowBudget` | `8` | Budget for one-shot (flow) tasks |
| `foreverBudget` | `4` | Budget for forever tasks’ generators |

```ts
initializer({
  adapter: 'nuxt',
  up: { thread: { flowBudget: 10, foreverBudget: 5 } }
})
```

### `up.event`

| Key | Meaning |
| --- | --- |
| `enhance` | Extra events usable with `Up.Event.sub` |
| `staticFilesConfig` | Options for the static-file wait before each `LOAD` |

**`enhance` entry shapes**

1. **String** — DOM event on `window`, no wait, default Thread flag (`128`).
2. **Object** — customize:
   - `key` — DOM event name (or any string)
   - `on` — `'window'` (default), `'document'`, or `'independent'` (no DOM listener)
   - `forever: true` — run subscribers every frame as a Thread forever task
   - `waitType` + `waitDelay` — `'debounce' | 'throttle'` + ms (not with `forever`)
   - `threadFlag` — Thread priority (power of 2); `0` runs inline (not with `forever`)
   - `argsTypes` — string TypeScript tuple for generated typings, e.g. `'[e: MouseEvent]'`

DOM listeners are always **passive**.

**`on: 'independent'`:** no `addEventListener`. Built-ins (`LIFE`, `TICK`, …) are fired by the runtime. For **custom** keys there is **no public `emit` API** — use `forever: true` for a custom per-frame cue, or attach a real DOM event. A non-forever independent custom key will never run from app code.

```ts
initializer({
  adapter: 'svelte',
  up: {
    event: {
      enhance: [
        'pointerdown',
        {
          key: 'mousemove',
          waitType: 'throttle',
          waitDelay: 50,
          argsTypes: '[e: MouseEvent]'
        },
        {
          key: 'MY_FRAME',
          on: 'independent',
          forever: true
        }
      ],
      staticFilesConfig: {
        viewportOnly: false
        // binaryMask / delay: see Wait — docs/scripts/wait.md
      }
    }
  }
})
```

**`staticFilesConfig`** (shallow-merged onto Event defaults `eagerOnly` / `viewportOnly` / `emptySizeOnly` = `true`):

| Key | Role |
| --- | --- |
| `eagerOnly` | Skip `loading="lazy"` images |
| `viewportOnly` | Skip elements not in viewport (`Is.visible`) |
| `emptySizeOnly` | Only await zero-size elements |
| `binaryMask` | Bitmask of kinds (`Wait.STATIC_FILES_*_FLAG`) |
| `delay` | Per-element timeout ms (Wait default `3000`) |
| `limiter` | **Do not set** — runtime forces `Adapter.root` |

Details: [Wait](scripts/wait.md).

### Generated types (augmentations)

After the first Vite run, edit **only** the customizable stub (never overwritten):

`<setupIn>/upwwwards/customizable/types/config.d.ts`

```ts
declare module 'upwwwards'
{
  export interface AppCache
  {
    heroEl: HTMLElement
    // Use static string keys only (SSR cache risk with dynamic keys)
  }
}

declare module 'upwwwards/plugins/Transmitter'
{
  import type { Transmitter } from 'upwwwards/plugins/Transmitter'

  export interface TransmitterChannels
  {
    // Config<async, message, returnCases>
    PING: Transmitter.Config<false, 'ping', 'pong'>
  }
}

declare module 'upwwwards/plugins/Synthra'
{
  export interface SynthraBucket
  {
    myKey: number
  }

  export interface SynthraPlugins
  {
    /* custom plugin registry keys */
  }
}

export {} /* IMPORTANT: DO NOT DELETE THIS LINE. */
```

Plugin-specific stubs are appended when you enable Transmitter / Synthra / …. Restart Vite if `private/types/upwwwards.d.ts` looks stale.

### Richer examples

**Nuxt** with plugins + Classter:

```ts
import initializer from 'upwwwards/initializers/vite'
import Vue from 'upwwwards/classter/Vue'

export default defineNuxtConfig({
  vite: {
    plugins: [
      initializer({
        adapter: 'nuxt',
        up: { thread: { flowBudget: 8 } },
        plugins: ['Scroll', { name: 'Screen', config: { dimensions: { mobileWidth: 768 } } }],
        classter: { Classter: Vue }
      })
    ]
  },
  css: ['classter.css'] // if Classter enabled
})
```

**SvelteKit** with Anime:

```ts
import { sveltekit } from '@sveltejs/kit/vite'
import { defineConfig } from 'vite'
import initializer from 'upwwwards/initializers/vite'
import Svelte from 'upwwwards/classter/Svelte'

export default defineConfig({
  plugins: [
    initializer({
      adapter: 'svelte',
      plugins: ['Observer', { name: 'Anime', config: { plugins: ['ScrollTrigger'] } }],
      classter: { Classter: Svelte }
    }),
    sveltekit()
  ]
})
```

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional. What the plugin does at build time.

- Rewrites `import Up from 'upwwwards'` to a **virtual module** that runs `Up[construct]` once (SSR + client) with your adapter, `up` config, and plugins.
- Writes under `<setupIn>/upwwwards/`:
  - `private/types/upwwwards.d.ts` — `Up.Adapter`, plugins, `AdapterConfig` / `AdapterRef`, `EventData` (rewritten when outdated)
  - `customizable/types/config.d.ts` — created **once** for you to augment
  - `private/styles/_screen.scss` — when `Screen` is enabled
- Optionally compiles **Classter** tokens into virtual `classter.css` (HMR in dev, injected in build).
- Anime `plugins` and Synthra `pick` are rewritten into lazy imports (not plain JSON).

Lifecycle symbols: [advanced/symbols](advanced/symbols.md).

---

## See also

- [Getting started](getting-started.md) · [Concepts](concepts.md)  
- [Adapters](adapters.md) · [Plugins](plugins.md) · [Classter](classter.md) · [Core](core.md)  
