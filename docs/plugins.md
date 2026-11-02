# Plugins

Plugins add optional features to `Up`. They are **opt-in** in the Vite plugin and only appear on `Up` when listed.

| Plugin | Access | Doc |
| --- | --- | --- |
| [Observer](plugins/observer.md) | `Up.Observer` | Resize / Intersection / Mutation |
| [Screen](plugins/screen.md) | `Up.Screen` | Layout format `C` / `M` / `T` / `D` |
| [Transmitter](plugins/transmitter.md) | `Up.Transmitter` | Typed request/response channels |
| [Scroll](plugins/scroll.md) | `Up.Scroll` | Smooth scroll (Lenis) |
| [Anime](plugins/anime.md) | `Up.Anime` | GSAP integration |
| [Puppeteer](plugins/puppeteer.md) | `Up.Puppeteer` | DOM helpers |
| [Synthra](plugins/synthra.md) | `Up.Synthra` | Three.js scene / X3D |

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### Enable plugins

List the plugins you want in the Vite plugin:

```ts
import initializer from 'upwwwards/initializers/vite'

initializer({
  adapter: 'nuxt', // or 'svelte'
  plugins: [
    'Scroll',
    { name: 'Screen', config: { defaultFormat: 'D' } },
    { name: 'Anime', config: { plugins: ['ScrollTrigger', 'SplitText'] } }
  ]
})
```

- String → enable with defaults: `'Observer'`
- Object → enable with config: `{ name: 'Screen', config: { … } }`

Then use them from `Up`:

```ts
Up.Scroll.to('#contact')
Up.Screen.get('M')
```

### Available plugins

| Plugin | Access | Peer | Lazy? | What it does |
| --- | --- | --- | --- | --- |
| [Observer](plugins/observer.md) | `Up.Observer` | — | No | Resize / Intersection / Mutation |
| [Screen](plugins/screen.md) | `Up.Screen` | — | No | Format `C` / `M` / `T` / `D` |
| [Transmitter](plugins/transmitter.md) | `Up.Transmitter` | — | No | Typed request/response channels |
| [Scroll](plugins/scroll.md) | `Up.Scroll` | `lenis` | Yes | Smooth scroll (Lenis) |
| [Anime](plugins/anime.md) | `Up.Anime` | `gsap` | Yes | GSAP integration |
| [Puppeteer](plugins/puppeteer.md) | `Up.Puppeteer` | — | No | DOM helpers |
| [Synthra](plugins/synthra.md) | `Up.Synthra` | `three` | Yes | Three.js scene / X3D |

Plugins with a **peer** need that package installed — see the [FAQ](faq.md#missing-peer-gsap--lenis--three).

> **Warning:** for lazy peers (`gsap`, `lenis`, `three`), install the package but **do not** `import` it at runtime yourself (TypeScript `import type` is fine). Use only the plugin APIs (`Up.Anime`, `Up.Scroll`, `Up.Synthra`, …) so Vite keeps the optimized lazy graph. Access is often asynchronous until first use.

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### Lazy plugins

**Lazy** plugins load their heavy dependency on first real use (e.g. first `workspace` / first `LIFE`), not at `Up.set`. Scroll, Anime, and Synthra are lazy; their peers (`lenis`, `gsap`, `three`) are only imported at that point — through the plugin, not via a direct app import.

### Naming notes

- **Anime** is the **GSAP** integration (animation workspaces and helpers). It is **not** anime.js.
- **Puppeteer** is a small set of DOM helpers. It is **not** Google Puppeteer or browser automation.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

### Lifecycle hooks

| Hook | When |
| --- | --- |
| `[construct]` | Once per process (SSR + client), with config |
| `[set]` | Client, from `Up.set`. May return a **Promise** — `Up.set` adds it to `App.addWait`, so **`LOAD` waits** for it |
| `[load]` | Lazy only, on first real use (e.g. first `workspace`) |
| `[destroy]` | Client, from `Up.destroy()` |

App code usually only calls the **public** APIs on `Up.<Plugin>`.

### Custom plugins and patching

Authoring custom plugins / subclassing: use the same symbol protocol as built-ins (`construct` / `set` / `load` / `destroy`) — [symbols](advanced/symbols.md). To swap a registered plugin for a subclass without forking: **`Up.patch`** — [core](core.md#uppatchpluginclass).

---

## See also

- [Getting started](getting-started.md) · [Concepts](concepts.md) · [Recipes](recipes.md)  
- [Core](core.md) · [Initializers](initializers.md) · [FAQ](faq.md)  
