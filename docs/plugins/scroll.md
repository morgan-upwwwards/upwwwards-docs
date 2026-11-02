# Scroll

Smooth scrolling via **[Lenis](https://github.com/darkroomengineering/lenis)**, loaded lazily and driven by `Up.Thread` (`autoRaf: false`).

| | |
| --- | --- |
| **Enable** | `plugins: ['Scroll']` |
| **Access** | `Up.Scroll` |
| **Peer** | `lenis` |
| **Lazy** | yes — Lenis loads on first `workspace` / first need |
| **Config** | none in the Vite plugin today (Lenis constructed with `{ autoRaf: false }` only) |

> **Warning — do not import `lenis` yourself** (except TypeScript `import type`). Manual runtime imports bypass the Vite lazy graph and can break build-time optimization. Use Lenis only through `Up.Scroll` APIs (`to`, `stop`, `start`, `workspace`, …) — access is often asynchronous until the peer has loaded.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

Install `lenis`, enable the plugin, restart Vite.

```ts
import Up from 'upwwwards'

Up.Scroll.to('#contact', { offset: -80 })
Up.Scroll.stop()  // e.g. modal open
Up.Scroll.start()
```

| Method | Role |
| --- | --- |
| `to(target, options?)` | Scroll to element / number / selector (Lenis `scrollTo` once loaded) |
| `stop` / `start` | Pause / resume the shared instance |

`to` / `stop` / `start` options match Lenis once loaded (`ScrollToOptions` for `to`). Safe no-ops / fallbacks before Lenis is ready.

Modal pause: [recipes](../recipes.md#modal-open--pause-smooth-scroll).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### `workspace`

Run code when Lenis is ready — prefer this over reading internals:

```ts
Up.Scroll.workspace(({ Lenis, global }) => {
  // Lenis = constructor; global = shared instance
  global.on('scroll', console.log)
})
```

### With Anime / ScrollTrigger

1. Enable **Scroll** + **Anime** with `ScrollTrigger` in Anime `plugins`.  
2. In `Anime.sub`, create tweens / `Timeline` as usual.  
3. After Lenis is up, connect it to ScrollTrigger yourself (Lenis docs, `ScrollTrigger.scrollerProxy`, or `ScrollTrigger.update()` on Lenis `scroll`).

> **Tip:** upwwwards does not wire Lenis and ScrollTrigger together automatically.

```ts
Up.Scroll.workspace(({ global }) => {
  global.on('scroll', () => {
    // e.g. ScrollTrigger.update() when using scrubbed triggers
  })
})
```

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- First **`LIFE`** → plugin calls `workspace` internally so Lenis starts loading (even if you never call `Up.Scroll` yourself).  
- **`LOAD`** → forever Thread task (flag `32`) drives Lenis each frame (`autoRaf: false`).  
- Client-only. Peer required on first real use — [FAQ](../faq.md#missing-peer-gsap--lenis--three).

---

## See also

- [Plugins index](../plugins.md)  
- [Anime](anime.md) · [Core](../core.md)  
