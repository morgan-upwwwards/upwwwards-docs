# upwwwards

**One shared page lifecycle, one frame loop, and opt-in plugins for Nuxt 4 and SvelteKit 2.**

Every feature in your app subscribes to the same cues — leave, enter, ready — and the same `requestAnimationFrame` budget, instead of each one wiring its own router hooks, scroll library, GSAP context, or render loop. Plugins (Scroll, Anime, Synthra, …) are optional; the core works on its own.

*(Think of it as a conductor: the framework is the stage, `Up` keeps everyone on the same beat.)*

**Free for commercial use** under a proprietary Standard “No Charge” license (not OSI open source). Details: [docs/advanced/license.md](docs/advanced/license.md) · [`LICENSE`](LICENSE)

**Requirements:** Nuxt 4 or SvelteKit 2 · Vite 7 · TypeScript recommended · Version `0.1.0-beta.1`

| You want… | upwwwards gives you… |
| --- | --- |
| Leave / enter / ready that matches the router | `Up.Event` + `Up.Adapter` |
| One `requestAnimationFrame` budget for the app | `Up.Thread` |
| Viewport metrics, cache, “wait before ready” | `Up.App` |
| GSAP / Lenis / Three without ad-hoc glue | Opt-in [plugins](docs/plugins.md) |
| The same spine on Nuxt *or* SvelteKit | Vite `adapter: 'nuxt' \| 'svelte'` |

---

## Install

```bash
npm install upwwwards
```

`pnpm add`, `yarn add`, and `bun add` work the same way.

Peer dependencies are optional and match what you enable (`gsap`, `lenis`, `three`, …). See [Getting started](docs/getting-started.md).

---

## Quick start

Two steps (Vite plugin, then root layout). After that you should see `LIFE` then `LOAD` in the console on first paint (and again after each client navigation).

**Glossary:** `DEATH` = leave · `LIFE` = enter · `LOAD` = ready. Build on `LIFE`, tear down on `DEATH`.

### Nuxt 4

**1 — Vite plugin** — `nuxt.config.ts`:

```ts
import initializer from 'upwwwards/initializers/vite'

export default defineNuxtConfig({
  vite: {
    plugins: [initializer({ adapter: 'nuxt' })]
  }
})
```

**2 — Start once** — `app/layouts/default.vue`:

```vue
<script lang="ts" setup>
import Up from 'upwwwards'

Up.set()

Up.Event.sub([
  ['DEATH', () => console.log('DEATH')],
  ['LIFE',  () => console.log('LIFE')],
  ['LOAD',  () => console.log('LOAD')]
])
</script>

<template>
  <slot />
</template>
```

### SvelteKit 2

**1 — Vite plugin** — `vite.config.ts`:

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

**2 — Start once** — `src/routes/+layout.svelte`:

```svelte
<script lang="ts">
  import { beforeNavigate, onNavigate, afterNavigate } from '$app/navigation'
  import Up from 'upwwwards'

  let { children } = $props()

  Up.set({ beforeNavigate, onNavigate, afterNavigate })

  Up.Event.sub([
    ['DEATH', () => console.log('DEATH')],
    ['LIFE',  () => console.log('LIFE')],
    ['LOAD',  () => console.log('LOAD')]
  ])
</script>

{@render children()}
```

If nothing logs on first paint, subscribe earlier (right after `Up.set`) — [getting-started — First load](docs/getting-started.md#first-load-note).

Full walkthrough + SSR: **[Getting started](docs/getting-started.md)** · Mental model: **[concepts](docs/concepts.md)**

---

## What you get

- **Common spine** — `Up.Adapter`, `Up.App`, `Up.Thread`, `Up.Event` on both hosts  
- **Predictable page cycle** — construct on `LIFE`, destroy on `DEATH`  
- **Opt-in plugins** — Scroll (Lenis), Anime (GSAP), Synthra (Three.js), Screen, Observer, Transmitter, Puppeteer (DOM helpers)  
- **Build-time wiring** — adapter selection, generated types, optional Classter CSS  
- **SSR helpers** — `upwwwards/contexts/nuxt` or `…/sveltekit`  
- **Standalone scripts** — `Wait`, `Logs`, `Maths`, … from `upwwwards/scripts` (no `Up.set` needed)

Compared to wiring router hooks + GSAP Context + Lenis yourself: one place to subscribe, one teardown story, one frame scheduler.

---

## Showcase (Anime)

**Optional** — needs `plugins: ['Anime']` and `npm install gsap`. Skip on a first read; more patterns: **[docs/recipes.md](docs/recipes.md)**.

```ts
import Up from 'upwwwards'

const KEY = Symbol('Hero')

function build() {
  Up.Anime.sub(KEY, ({ gsap }) => {
    gsap.from('.hero', { opacity: 0, y: 40, duration: 0.8 })
  })
}

function teardown() {
  Up.Anime.unsub(KEY)
}

Up.Event.sub([
  ['LIFE', build],
  ['DEATH', teardown]
])
```

---

## Documentation

Docs live in this repo (`docs/`, this README, [`llms.txt`](llms.txt)). A hosted site and changelog are planned.

| Path | Start here if… |
| --- | --- |
| [docs/getting-started.md](docs/getting-started.md) | You are wiring a project today |
| [docs/concepts.md](docs/concepts.md) | You want the mental model |
| [docs/core.md](docs/core.md) | You need `App` / `Thread` / `Event` detail |
| [docs/recipes.md](docs/recipes.md) | You want copy-paste patterns |
| [docs/faq.md](docs/faq.md) | Something behaves unexpectedly |
| [docs/compatibility.md](docs/compatibility.md) | Host / peer version matrix |
| [docs/plugins.md](docs/plugins.md) | You enable Scroll, Anime, Synthra, … |
| [docs/scripts.md](docs/scripts.md) | Standalone utilities |
| [docs/index.md](docs/index.md) | Full map |

API pages use 🟢 **Essentials** · 🟡 **Going further** · ⬛ **Under the hood** — only Essentials is required.

Layers: [initializers](docs/initializers.md) · [adapters](docs/adapters.md) · [contexts](docs/contexts.md) · [classter](docs/classter.md) · [symbols](docs/advanced/symbols.md)

## License

See the summary at the top, [`LICENSE`](LICENSE), and [docs/advanced/license.md](docs/advanced/license.md).

## For AI agents

[`llms.txt`](llms.txt) → [getting-started](docs/getting-started.md) → [concepts](docs/concepts.md) → [core](docs/core.md). Prefer public entry points; don’t assume MIT/Apache rights.
