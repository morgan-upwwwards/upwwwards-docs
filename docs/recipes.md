# Recipes

Copy-paste patterns on top of [getting-started](getting-started.md) and [core](core.md).

🟢 **Essentials** — core (or one light plugin). 🟡 **Going further** — needs peers / richer APIs; skip until you enable those plugins.

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### Delay `LOAD` until fonts (or any promise)

```ts
Up.Event.sub([
  [
    'LIFE',
    () => {
      Up.App.addWait(document.fonts.ready)
      // Up.App.addWait(fetch('/api/boot'))
    }
  ],
  [
    'LOAD',
    () => {
      /* fonts / waits settled */
    }
  ]
])
```

`LOAD` also waits on static files after `LIFE`. Options: `up.event.staticFilesConfig` in [initializers](initializers.md).

### Component-scoped subscriptions (Nuxt / Vue)

```vue
<script lang="ts" setup>
import { onMounted, onUnmounted } from 'vue'
import Up from 'upwwwards'

function onDeath() { /* … */ }
function onLife() { /* … */ }

onMounted(() => {
  Up.Event.sub([
    ['DEATH', onDeath],
    ['LIFE', onLife]
  ])
})

onUnmounted(() => {
  Up.Event.unsub([
    ['DEATH', onDeath],
    ['LIFE', onLife]
  ])
})
</script>
```

Keep the **same function references** for `unsub`. Svelte: [core — Minimal page pattern](core.md#minimal-page-pattern).

### Per-frame work with `TICK`

```ts
Up.Event.sub(['TICK', (t) => { /* Thread.timestamp */ }])
```

Prefer this over raw Thread for simple work. Thread forever: [Going further](#tick-vs-thread-forever).

### Custom Event listener on an element

```ts
const onClick = (e: MouseEvent) => { /* … */ }

Up.Event.sub(['click', onClick, Up.Event.PASSIVE], document.body)
Up.Event.unsub(['click', onClick], document.body)
```

— [core — Custom DOM target](core.md#custom-dom-target).

### Screen-driven UI (Vue / Nuxt)

Requires [Screen](plugins/screen.md).

```vue
<script lang="ts" setup>
import Up from 'upwwwards'

const $mobile = Up.Screen.get('M') // Vue ref
</script>

<template>
  <nav v-if="!$mobile">…desktop…</nav>
  <nav v-else>…mobile…</nav>
</template>
```

### Screen-driven UI (SvelteKit)

```svelte
<script lang="ts">
  import Up from 'upwwwards'
  const $mobile = Up.Screen.get('M') // Svelte writable
</script>

{#if !$mobile}
  <!-- desktop -->
{:else}
  <!-- mobile -->
{/if}
```

### Modal open → pause smooth scroll

Requires [Scroll](plugins/scroll.md) (`lenis`).

```ts
function openModal() {
  Up.Scroll.stop()
}

function closeModal() {
  Up.Scroll.start()
}
```

### Missing peer dependency

```bash
npm install gsap    # Anime
npm install lenis   # Scroll
npm install three   # Synthra
```

List the plugin in the Vite `plugins` option and **restart** the dev server — [FAQ](faq.md).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional. Enable the matching plugin / peer first.

### Page enter / leave with Anime

Enable `plugins: ['Anime']` and install `gsap`. Construct on `LIFE`, tear down on `DEATH`.

```ts
import Up from 'upwwwards'

const KEY = Symbol('PageHero')

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

Up.Adapter.sub([['ABORTED', teardown]])
```

Also: [README showcase](../README.md#showcase-anime) · [Anime](plugins/anime.md).

### Nuxt `pageTransition` + lifecycle

With `app.pageTransition`, after the first `page:transition:finish` the Nuxt adapter can drive `AFTER_NAVIGATE` from that hook so `LIFE` waits for the transition.

```ts
Up.Event.sub([
  ['DEATH', () => { /* tear down leaving page */ }],
  ['LIFE', () => { /* construct enter work */ }]
])
```

Details: [adapters](adapters.md#host-caveats).

### Transmitter channel

Enable `Transmitter`, augment types, then:

```ts
Up.Transmitter.sub({
  channel: 'PING',
  force: true,
  receiver: (message: 'ping') => 'pong' as const
})

const reply = Up.Transmitter.broadcast('PING', 'ping')
Up.Transmitter.unsub({ channel: 'PING' })
```

[Transmitter](plugins/transmitter.md).

### Observer resize

```ts
function onResize(entry: ResizeObserverEntry) {
  /* … */
}

Up.Observer.sub(el, [['RESIZE', onResize]])
Up.Observer.unsub(el, [['RESIZE', onResize]])
```

[Observer](plugins/observer.md).

### Synthra page-scoped scene

```ts
let pack: { clear(): void } | null = null

function build() {
  Up.Synthra.workspace(({ three, X3D, Package, Plugin }) => {
    const cube = new X3D({
      root: new three.Mesh(new three.BoxGeometry(), new three.MeshNormalMaterial())
    })
    cube.setPosition(0, 0, -3)
    pack = new Package(
      cube,
      new (Plugin.get('BASIC'))({
        eTick: () => cube.setRotation(cube.rX, cube.rY + 0.01, cube.rZ)
      })
    )
  })
}

function teardown() {
  pack?.clear()
  pack = null
}

Up.Event.sub([
  ['LIFE', build],
  ['DEATH', teardown]
])
```

[Synthra](plugins/synthra.md).

### Classter token in a template

```html
<div class="di:flex ju-co:center pa:1rem ba-co:[#111] co:[#fff]" />
```

[classter](classter.md) · [aliases](classter-aliases.md).

### `TICK` vs Thread forever

```ts
Up.Event.sub(['TICK', (t) => { /* … */ }])

function frame() { /* … */ }
Up.Thread.markForever(frame)
Up.Thread.sub(frame)
```

Do **not** use bare `Up.Thread.sub(frame)` expecting a loop — [core — Thread](core.md#upthread--when-tick-is-not-enough).

---

## See also

- [Getting started](getting-started.md) · [Concepts](concepts.md) · [FAQ](faq.md)  
- [Core](core.md) · [Plugins](plugins.md) · [Documentation index](index.md)  
