# Screen

Tracks the active **layout format** as reactive adapter refs: crushed `C`, mobile `M`, tablet `T`, desktop `D`.

| | |
| --- | --- |
| **Enable** | `plugins: ['Screen']` or with config (see [Going further](#going-further)) |
| **Access** | `Up.Screen` |
| **Peers** | none |

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

> **Warning — hydration mismatches.** SSR seeds the store from a cookie or `defaultFormat`. The client may then run `matchMedia` before hydration finishes and flip refs, which Vue / Svelte report as a mismatch. See [Hydration mismatches](#hydration-mismatches).

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
      firstUpdateDelayed: true, // optional — see Hydration mismatches
      dimensions: {
        crushedHeight: 435, // default
        mobileWidth: 768,
        tabletWidth: 1440
      }
    }
  }
]
```

| Option | Default | Role |
| --- | --- | --- |
| `defaultFormat` | `'D'` | First visit / invalid cookie / no media match; also the SSR seed when nothing else is known |
| `firstUpdateDelayed` | `false` | Defer the first sync until `LIFE` **only when** seed ≠ viewport (see below) |
| `dimensions.*` | `435` / `768` / `1440` | Breakpoints in CSS px (`mobileWidth` must be ≤ `tabletWidth`) |

`defaultFormat` should match the layout most of your audience sees on first paint.

### CSS variables and SCSS

CSS variables on `:root`: `--upwwwards-screen-crushed-height`, `--upwwwards-screen-mobile-width`, `--upwwwards-screen-tablet-width`. The Vite plugin can also emit a `_screen.scss` partial from the same breakpoints.

### Hydration mismatches

The server never knows the real viewport. It seeds Screen from:

1. Cookie `upwwwards.plugins.screen.format` (last client format), or  
2. `defaultFormat` when the cookie is missing or invalid.

By default (`firstUpdateDelayed: false`), `Up.set` immediately syncs with `matchMedia`. If that format differs from the SSR seed **before** hydration completes, branching on `Up.Screen.get(…)` (`v-if` / `{#if}`) can throw a hydration mismatch.

**How to reduce or avoid it**

| Approach | Effect |
| --- | --- |
| Sensible `defaultFormat` | Fewer first-visit surprises when there is no cookie yet |
| Returning visitors + cookie | SSR usually already matches the last known format |
| `firstUpdateDelayed: true` | Defers only when seed ≠ `matchMedia`: keeps the seed through hydration, then syncs (+ resize modifier) on the first `LIFE`. If they already match, syncs immediately (no flash). Also syncs immediately if `LIFE` has already started (e.g. late re-`set`) |
| Soft first paint | Prefer CSS that tolerates a short wrong format over hard `v-if` / `{#if}` swaps on critical chrome |

Trade-off when deferral actually runs: a possible **flash** between SSR paint and `LIFE`. Prefer that over a mismatch when format-driven markup is critical.

Wire the [SSR context](../contexts.md) so the server can read the cookie and keep a request-scoped store.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

### Cookie & SSR

| Detail | Value |
| --- | --- |
| Cookie name | `upwwwards.plugins.screen.format` |
| Lifetime | ~1 year (updated when the client format changes) |
| First visit / invalid cookie | Uses `defaultFormat` (config; default `'D'`) |
| Secure flag | `true` on HTTPS (and on the server); `false` on plain HTTP so local HTTP still stores the cookie |

**SSR:** the store is seeded from that cookie (or `defaultFormat`). Without a [context](../contexts.md), server cookie / store access is a no-op or unsafe.

**Client sync:** `Up.set` compares the seed (valid cookie, else `defaultFormat`) to `matchMedia`. Default path always updates immediately. With `firstUpdateDelayed: true`, if they differ and `LIFE` has not started (`Up.Event.PRE_LIFE`), Screen subscribes once to `LIFE`, then registers the Thread resize modifier and applies `matchMedia`; if they already match (or `LIFE` already started), it syncs immediately like the default path. Later viewport changes stay on that resize modifier.

This is **layout-format** persistence, not the unused `hydrate` symbol in [symbols](../advanced/symbols.md).

---

## See also

- [Plugins index](../plugins.md)  
- [Adapters](../adapters.md) — `AdapterRef`  
- [Contexts](../contexts.md) — SSR store  
- [FAQ — Screen hydration mismatch](../faq.md#screen-hydration-mismatch--format-wrong-on-first-paint)