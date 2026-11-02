# Getting started

Wire **upwwwards** into **Nuxt 4** or **SvelteKit 2**, then subscribe to the page cycle.

**Minimal path** (about 5 minutes): install → Vite plugin → `Up.set` → `Up.Event.sub`. Everything else — SSR cookies, plugins, Classter — is optional.

Want the mental model first? [Concepts](concepts.md). Copy-paste patterns: [recipes](recipes.md).

---

## 1. Install

```bash
npm install upwwwards
# pnpm add upwwwards
# yarn add upwwwards
# bun add upwwwards
```

| Peer | When |
| --- | --- |
| `vite` | Always (initializer) |
| `nuxt` + `vue` | Nuxt |
| `@sveltejs/kit` + `svelte` | SvelteKit |
| `h3` | Optional — SSR [contexts](contexts.md) |
| `gsap` / `lenis` / `three` | Optional — Anime / Scroll / Synthra |

Install plugin peers **before** first use of that plugin, but do **not** import `gsap` / `lenis` / `three` at runtime yourself (except `import type`) — use `Up.Anime` / `Up.Scroll` / `Up.Synthra` only — [FAQ](faq.md#missing-peer-gsap--lenis--three).

---

## 2. Vite plugin

### Nuxt — `nuxt.config.ts`

```ts
import initializer from 'upwwwards/initializers/vite'

export default defineNuxtConfig({
  vite: {
    plugins: [
      initializer({
        adapter: 'nuxt'
        // plugins: ['Scroll', 'Anime']
      })
    ]
  }
})
```

### SvelteKit — `vite.config.ts`

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

After the first Vite run, types are written under `setupIn` (defaults: Nuxt `/app`, SvelteKit `/src/lib`). More options: [initializers](initializers.md).

---

## 3. `Up.set` once (root layout)

Call it once in the browser. A second call is a no-op.

### Nuxt — e.g. `app/layouts/default.vue`

```vue
<script lang="ts" setup>
import Up from 'upwwwards'

const router = useRouter()
const { hook } = useNuxtApp()

Up.set({ hook, router })
</script>

<template>
  <slot />
</template>
```

### SvelteKit — `src/routes/+layout.svelte`

```svelte
<script lang="ts">
  import { beforeNavigate, onNavigate, afterNavigate } from '$app/navigation'
  import Up from 'upwwwards'

  let { children } = $props()

  Up.set({ beforeNavigate, onNavigate, afterNavigate })
</script>

{@render children()}
```

---

## 4. Smoke test — subscribe

`DEATH` = leave · `LIFE` = enter · `LOAD` = ready.

Register **right after** `Up.set` (same layout file) so you catch the first enter:

```ts
import Up from 'upwwwards'

Up.Event.sub([
  ['DEATH', () => console.log('DEATH')],
  ['LIFE',  () => console.log('LIFE')],
  ['LOAD',  () => console.log('LOAD')]
])
```

**It works if:** on first paint you see `LIFE` then `LOAD` in the console. Navigate to another page: `DEATH` → `LIFE` → `LOAD`.

You can stop here — this is a working setup. Replace the logs with your real setup / teardown.

### First load note

On the **first paint** (and hard refresh) nothing is leaving, so there is no `DEATH` — only `LIFE` → `LOAD`. Late component mounts can miss that first enter; they still get later navigations.

Optional navigation phases (`BEFORE` / `ON` / `AFTER` / `ABORTED`): [adapters](adapters.md) · [concepts](concepts.md#page-cycle).

---

## Optional extras

None of these are required for the minimal path.

### SSR context (server cookies)

**Nuxt** — `server/plugins/upwwwards-context.ts`:

```ts
export { default } from 'upwwwards/contexts/nuxt'
```

**SvelteKit** — `src/hooks.server.ts`:

```ts
import { sequence } from '@sveltejs/kit/hooks'
import sveltekitHandle from 'upwwwards/contexts/sveltekit'

export const handle = sequence(sveltekitHandle)
```

→ [contexts](contexts.md)

### Plugins / Classter

Enable via the initializer `plugins` / `classter` options → [plugins](plugins.md) · [classter](classter.md)

---

## Checklist

**Minimal**

- [ ] Package installed  
- [ ] `initializer({ adapter: 'nuxt' | 'svelte' })`  
- [ ] `Up.set(…)` once in the root layout  
- [ ] `Up.Event.sub` — console shows `LIFE` / `LOAD`  

**Optional**

- [ ] Peers for plugins you enable  
- [ ] SSR context if you need server cookies  

## Next

- [Concepts](concepts.md) · [Core](core.md) (start at **Essentials**) · [Recipes](recipes.md) · [FAQ](faq.md)  
- [Documentation index](index.md)  
