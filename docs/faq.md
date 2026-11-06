# FAQ & troubleshooting

## Setup

### Nothing logs / `LIFE` never ran

Subscribe **right after** `Up.set` in the root layout (see [getting-started — Smoke test](getting-started.md#4-smoke-test--subscribe)). On first paint you should see `LIFE` then `LOAD`. Late component mounts miss the first enter; they still get later navigations.

### `Up` has no `Scroll` / `Anime` / …

Plugins only appear after you list them in the Vite initializer and restart the dev server (types are generated under `setupIn/upwwwards/`).

```ts
initializer({ adapter: 'nuxt', plugins: ['Scroll', 'Anime'] })
```

Install the peer (`lenis`, `gsap`, `three`, …) for that plugin.

### Missing peer (`gsap` / `lenis` / `three`)

Lazy plugins load peers on **first real use**. If the package is not installed, that first call fails (import / runtime error). Install the peer, but **do not** import it at runtime in your app (except `import type`) — use `Up.Anime` / `Up.Scroll` / `Up.Synthra` only, or you can break build-time lazy optimization. Fix:

```bash
npm install gsap    # Anime
npm install lenis   # Scroll
npm install three   # Synthra
```

Using pnpm, yarn, or bun? Use `pnpm add`, `yarn add`, or `bun add` with the same package names.

Then restart Vite. See [recipes](recipes.md#missing-peer-dependency).

### `Up.set` seems to do nothing the second time

Expected: the first call wires the runtime; later calls are no-ops. Call it **once** from the root layout.

### Types for `Up.Adapter` / plugins are wrong or missing

Run Vite once so the plugin can write `private/types/upwwwards.d.ts`. Augment app-specific shapes in `customizable/types/config.d.ts` (created once) — [example](initializers.md#generated-types-augmentations).

### Do I need the SSR context?

Only if you use Adapter cookies or request-scoped stores (`Up.App` cache, `Up.Screen`, …) on the server. Browser-only apps can skip it.

→ [contexts](contexts.md)

---

## Navigation & lifecycle

### `DEATH` never fires

`DEATH` runs on Adapter **`ON_NAVIGATE`**. Soft cancellations that never commit the nav do not emit it. Hard failures after start emit **`ABORTED`** instead of `AFTER` / `LIFE` / `LOAD`.

**First load / hard refresh** also has no `DEATH` — only `LIFE` / `LOAD`. See [getting-started](getting-started.md#first-load-note).

### `LIFE` / `LOAD` skipped after a failed navigation

Listen to `Up.Adapter` **`ABORTED`** and run the same teardown as `DEATH` if you already constructed something in an earlier phase (unusual) or need to unlock UI from `BEFORE_NAVIGATE`.

### Nuxt: I aborted in middleware but saw no `BEFORE` / `ABORTED`

If middleware calls `abortNavigation()` **before** the adapter has wired navigation, the Adapter cycle never starts. Guards registered **after** `Up.set` can abort after `BEFORE`/`ON`.

→ [adapters](adapters.md)

### Nuxt: `AFTER_NAVIGATE` timing with page transitions

With `app.pageTransition` (including `out-in`), `AFTER` / `LIFE` still wait until the new page is ready — nothing extra to configure. Call `Up.set({ nuxtApp: useNuxtApp() })` once in a root layout setup.

### SvelteKit: when do I pass `beforeNavigate` / `onNavigate` / `afterNavigate`?

During component init in the root `+layout.svelte` (same pattern as calling those APIs yourself). See [getting-started](getting-started.md).

---

## Runtime

### `LOAD` fires too early

Register promises with `Up.App.addWait(…)` (e.g. during `LIFE`, before subscribers return). `LOAD` also waits on static-file readiness (`Wait.staticFiles`). Tune via `up.event.staticFilesConfig` — [initializers](initializers.md#upevent).

### My `Thread.sub(frame)` only ran once

Expected: default tasks are **flow** (one-shot). For every frame, `Up.Thread.markForever(frame)` **before** `sub`, or use `Up.Event.sub(['TICK', frame])`. Default flag is `4096` (`MAX_FLAG`), not Event’s `128`. — [core — Thread](core.md#upthread--when-tick-is-not-enough).

### `Up.destroy()` broke the app / HMR

`destroy` is **irreversible**: `Up.set` cannot restart the runtime. Call once for a full shutdown only. — [core — Under the hood](core.md#runtime-boot).

### Memory leaks after navigation

Always **unsub** with the **same function references** you passed to `Event.sub` / `Adapter.sub` / `Thread.sub` / `Observer.sub` / `Anime.unsub(key)`. Cancel `Wait.throttle` / `debounce` wrappers with `Wait.cancel`.

### Can I use scripts without the Vite plugin?

Yes — `import { Colors, Wait } from 'upwwwards/scripts'`. You do **not** get `Up.Event` / plugins without the initializer + `Up.set`. Deep script paths exist only for `Trees` / `Arrays` — [compatibility](compatibility.md#package-exports).

### `Cookies` throws on the server

Expected (`document` missing). Use `Up.Adapter.setCookie` / `getCookie` / `destroyCookie` with a [context](contexts.md).

### Screen format wrong on first SSR paint

Cookie `up::plugins::screen::format` may be missing on first visit → `defaultFormat`. Wire SSR context; expect a possible client sync after `Up.set`. — [Screen](plugins/screen.md#cookie--ssr).

---

## License & packaging

### Is this open source?

**Free for commercial use** within the license’s Permitted Use. It is not OSI open source: upwwwards ships under a proprietary **Standard “No Charge”** license, which restricts competing forks and republishing as a rival library. See [license summary](advanced/license.md) and [`LICENSE`](../LICENSE).

### Can my npm package depend on upwwwards?

Yes, if it is not a Competitive Product (substitute for the library itself). Details in the LICENSE FAQ.

### Where is the documentation site / changelog?

Use this `docs/` folder, the [README](../README.md), and [`llms.txt`](../llms.txt). A hosted docs site and a changelog are planned. Until then, versions follow the published `upwwwards` package version on npm / git tags. Support matrix: [compatibility](compatibility.md).

---

## Still stuck?

1. Confirm `adapter` matches the host (`'nuxt'` / `'svelte'`).  
2. Confirm a single `Up.set` in the root layout, then an early `Up.Event.sub` (smoke: `LIFE` / `LOAD` in the console).  
3. Check [concepts](concepts.md) for the page cycle.  
4. Skim host-specific notes in [adapters](adapters.md).  
5. Try an [Essentials recipe](recipes.md#essentials).  
