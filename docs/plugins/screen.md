# Screen

Tracks the active **layout format** as reactive adapter refs: crushed `C`, mobile `M`, tablet `T`, desktop `D`.

| | |
| --- | --- |
| **Enable** | `plugins: ['Screen']` or with config (see [Going further](#going-further)) |
| **Access** | `Up.Screen` |
| **Peers** | none |

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### Formats

| Code | Meaning |
| --- | --- |
| `C` | Crushed (viewport height below `crushedHeight`) |
| `M` | Mobile (width below `mobileWidth`); also true while `C` |
| `T` | Tablet (`[mobileWidth, tabletWidth)`) |
| `D` | Desktop (`≥ tabletWidth`) |

### Read a format

```ts
const $mobile = Up.Screen.get('M')  // AdapterRef<boolean>
const $desktop = Up.Screen.get('D')
```

`get` works on server and client. On the server, wire the [SSR context](../contexts.md) so the store is request-scoped.

### Nuxt / Vue — unwrap the ref in the template

`Up.Screen.get` returns a Vue `ref`. Auto-unwrap in `<template>`; use `.value` in script.

```vue
<script lang="ts" setup>
import Up from 'upwwwards'

const $mobile = Up.Screen.get('M')

if ($mobile.value) {
  /* script-side check */
}
</script>

<template>
  <aside v-if="$mobile">Mobile chrome</aside>
  <aside v-else>Desktop chrome</aside>
</template>
```

### SvelteKit — subscribe / `$` store syntax

`Up.Screen.get` returns a Svelte `writable`. Use `$store` in the template.

```svelte
<script lang="ts">
  import Up from 'upwwwards'
  const $mobile = Up.Screen.get('M')
</script>

{#if $mobile}
  <aside>Mobile chrome</aside>
{:else}
  <aside>Desktop chrome</aside>
{/if}
```

More: [recipes — Screen](../recipes.md#screen-driven-ui-vue--nuxt).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### Configure breakpoints

```ts
plugins: [
  {
    name: 'Screen',
    config: {
      defaultFormat: 'D',
      dimensions: {
        crushedHeight: 435, // default
        mobileWidth: 768,
        tabletWidth: 1440
      }
    }
  }
]
```

`defaultFormat` is used on a first visit (no cookie) and during SSR when nothing else is known — pick a sensible value for your audience.

### CSS variables and SCSS

CSS variables on `:root`: `--up-screen-crushed-height`, `--up-screen-mobile-width`, `--up-screen-tablet-width`. The Vite plugin can also emit a `_screen.scss` partial from the same breakpoints.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

### Cookie & SSR

| Detail | Value |
| --- | --- |
| Cookie name | `up::plugins::screen::format` |
| Lifetime | ~1 year (updated when the client format changes) |
| First visit / invalid cookie | Uses `defaultFormat` (config; default `'D'`) |

**SSR:** the store is seeded from that cookie (or `defaultFormat`) so markup can match the last known client format. Wire the [SSR context](../contexts.md) so request-scoped Screen state is safe on the server. Without a context, server cookie / store access is a no-op or unsafe.

**Hydration:** after `Up.set`, the client re-syncs with `matchMedia` and Thread resize frames. A first visit (no cookie) or a changed viewport can briefly disagree with SSR until that sync — prefer a sensible `defaultFormat` and avoid layout that hard-depends on the wrong breakpoint for the first paint.

This is **layout-format** persistence, not the unused `hydrate` symbol in [symbols](../advanced/symbols.md).

---

## See also

- [Plugins index](../plugins.md)  
- [Adapters](../adapters.md) — `AdapterRef`  
- [Contexts](../contexts.md) — SSR store  
