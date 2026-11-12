# Anime — GSAP plugin

**GSAP integration** for upwwwards: named workspaces (`sub` / `unsub`), optional GSAP plugins, and helpers (`Timeline`, `Tweens`, `Split`). Not related to anime.js.

| | |
| --- | --- |
| **Enable** | `plugins: ['Anime']` (GSAP plugins optional — Going further) |
| **Access** | `Up.Anime` |
| **Peer** | `gsap` |
| **Lazy** | yes — GSAP (+ listed plugins) load on first `workspace` / `sub` |

> **Warning — do not import `gsap` yourself** (except TypeScript `import type`). Manual runtime imports bypass the Vite lazy graph and can break build-time optimization. Use GSAP only through `Up.Anime` APIs (`sub`, `workspace`, …) — access is often asynchronous until the peer has loaded.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### Enable

Minimal — GSAP core only:

```ts
plugins: ['Anime']
```

Install `gsap`, restart Vite. Add GSAP plugins / easings when you need them — [Going further](#enable-with-gsap-plugins).

### Workspaces

Use one **symbol key** per component (or logical scope). Everything created synchronously inside `sub` is tracked in a GSAP context and cleaned on `unsub`.

```ts
import Up from 'upwwwards'

const KEY = Symbol('Hero')

Up.Anime.sub(KEY, ({ gsap }) => {
  gsap.to('.hero', { opacity: 1, duration: 0.6 })
})

// on leave / unmount
Up.Anime.unsub(KEY)
```

### Pattern with page lifecycle

```ts
Up.Event.sub([
  ['LIFE', () => Up.Anime.sub(KEY, setup)],
  ['DEATH', () => Up.Anime.unsub(KEY)]
])
```

Also: [recipes — Anime](../recipes.md#page-enter--leave-with-anime).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### Enable with GSAP plugins

```ts
plugins: [
  {
    name: 'Anime',
    config: {
      plugins: ['CustomEase', 'ScrollTrigger', 'SplitText', 'Flip'],
      easings: {
        // requires CustomEase
        // myEase: 'M0,0 C0.5,0 0.5,1 1,1'
      }
    }
  }
]
```

### Bucket highlights

Inside `sub`, the callback receives:

| Key | Role |
| --- | --- |
| `gsap` | GSAP instance |
| `workspace` | Current `sub` scope (`ctx`, `cleaningStack`, your extras) |
| Plugin names | e.g. `ScrollTrigger`, `SplitText` when enabled |
| Helpers | `Timeline`, `Tweens`, `Split` (classes on the bucket) |

`workspace(cb)` runs against the loaded bucket without opening a keyed scope (or with an empty workspace object outside `sub`).

Do not put custom data under reserved keys `ctx` / `cleaningStack`.

### Helper: `Tweens`

Standalone GSAP tweens / timelines in a private context. Built inside `sub` → auto-cleaned on `unsub`.

| Method | Role |
| --- | --- |
| `add(type, target, vars)` / `add('F/T', target, fromVars, toVars)` | Chainable; `type`: `'F'` / `'T'` / `'F/T'` |
| `addTimeline(…)` | Nested timeline helpers (see JSDoc) |
| `destroy()` | Revert the private context |

```ts
Up.Anime.sub(KEY, ({ Tweens }) => {
  const tweens = new Tweens()
  tweens
    .add('T', '.card', { y: 0, stagger: 0.1, scrollTrigger: { trigger: '.cards', scrub: true } })
    ('F/T', '.title', { autoAlpha: 0 }, { autoAlpha: 1, duration: 0.6 })
})
```

### Helper: `Split`

Requires `'SplitText'` in Anime `plugins`. Owns **one** `SplitText` per target in a private GSAP context; `destroy` restores markup. Auto-cleaned inside `sub`.

`add(target, vars)` always returns a **`Split.SPLIT`** (`types`, `mask`, `chars` / `lines` / `words` / `masks`, `splitText`) — not a raw `SplitText`. On the **same target**, it is the **same object**: element arrays keep their references and are refreshed in place after a re-split.

| Call | Behavior |
| --- | --- |
| Subset of cached types (+ compatible `mask`) | Return cache, no re-split |
| New types and/or new `mask` | **Merge** types, `splitText.split(…)`, refresh arrays |
| `reconstruct: true` | **Replace** cached types / mask, re-split, refresh arrays |

`reconstruct` is upwwwards-only (not forwarded to GSAP). Tweens already targeting old DOM nodes stay stale after an expand / reconstruct — re-read `text.lines` / `text.words` (or rebuild tweens) if needed.

```ts
Up.Anime.sub(KEY, ({ gsap, Split }) => {
  const split = new Split()
  const text = split.add('.hero__title', { type: 'lines,words' })
  gsap.from(text.words, { yPercent: 100, stagger: 0.05 })

  // same object: union types + re-split in place
  split.add('.hero__title', { type: 'lines,words,chars' })

  // same object: subset → reuse, no re-split
  split.add('.hero__title', { type: 'words' })

  // same object: replace definition + re-split
  split.add('.hero__title', { type: 'words', reconstruct: true })
})
```

### Helper: `Timeline`

Requires `'ScrollTrigger'`. One ScrollTrigger drives progress `0`–`1`:

```
[ start overflow ][ main band (optional fragments) ][ end overflow ]
```

The trigger should be **taller than the viewport** (`100lvh`); otherwise the range is empty or inverted.

#### Config

| Option | Role |
| --- | --- |
| ScrollTrigger vars | `trigger`, `scrub`, `pin`, … (standard GSAP) |
| `start` / `end` | Fractions of **trigger height** (`0`–`1`, defaults `0` / `1`) — not GSAP string positions |
| `getOverflowStart` / `getOverflowEnd` | `() => number` px; re-read on refresh |
| `eEnter` / `eEnterBack` / `eLeave` / `eLeaveBack` | Replaces forbidden native `onEnter` / … |
| `eUpdate(progress)` | Progress `0`–`1` after track callbacks |
| `eRefresh` | After each ScrollTrigger refresh, once overflows / animations / tracks are recomputed |
| `name` | Debug label |

Native `onEnter` / `onUpdate` / `onRefresh` / … are **typed as `never`** — use the `e*` hooks.

#### Placement

Four mutually exclusive forms (fractions `0`–`1`):

| Form | Meaning |
| --- | --- |
| `start` + `end` | On the main band, or inside a `fragment` |
| `overflowStart` + `end` | From start overflow into the main band |
| `start` + `overflowEnd` | From main band into end overflow |
| `overflowStart` + `overflowEnd` | Span both overflows |

`fragment`: **1-based** index from `addFragment({ flex })` (`flex > 0`).

**`addAnimation`:** types `'F'` / `'T'` / `'F/T'`. Do **not** set `duration` — derived from placement. Chainable.

**`addTrack`:** logical segment without a tween (`eEnter` / `eLeave` / `eUpdate` / `eDestroy`). Higher `priority` wins when several tracks update one object; `skipped: true` means the track was jumped — apply final state without animating.

**`update()`:** waits for pending fragment updates and the first ScrollTrigger refresh, then forces a track re-evaluation from the **last notified** progress to the live scrub progress (e.g. after adding tracks). Mid-scroll restore catch-up from `0` is done on that first refresh, not by `update()` itself. Does nothing if `Up.App.y` is still before the timeline start. Warns and returns if the timeline is not configured.

```ts
Up.Anime.sub(KEY, ({ Timeline }) => {
  const timeline = new Timeline({
    trigger: '.story',
    scrub: true,
    getOverflowStart: () => Up.App.lh
  })

  const INTRO = timeline.addFragment({ flex: 1 })
  const OUTRO = timeline.addFragment({ flex: 2 })

  timeline
    .addAnimation('F', '.story__title', { autoAlpha: 0, end: 0, overflowStart: 1 })
    ('T', document.querySelectorAll('.story__item'), {
      x: 100,
      fragment: INTRO,
      start: 0,
      end: 1,
      stagger: 0.1
    })

  timeline.addTrack({
    fragment: OUTRO,
    start: 0,
    end: 1,
    eUpdate: (progress) => { /* … */ }
  })
})
```

With Lenis: [Scroll — Going further](scroll.md#with-anime--scrolltrigger).

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

- The Vite plugin rewrites Anime `plugins` into dynamic `import('gsap/<Name>')` loaders and augments bucket types.  
- Clock: GSAP’s ticker drives `Up.Thread`, or — if Thread already has an external ticker — GSAP sleeps and `gsap.updateRoot` runs as a forever Thread task on **`MAX_FLAG` (`32768`)** (after drivers, modifiers, and lower-flag tasks).  
- With **Scroll** enabled, Lenis arms flag `32` so ScrollTrigger modifiers stay in sync without a manual Lenis `scroll` listener.  
- `unsub` runs `cleaningStack` in reverse, then `ctx.revert()`.  
- Helpers created inside `sub` push destroyers onto `workspace.cleaningStack`.  
- **`Timeline` refresh gate:** track/`eUpdate` handling is deferred until the first ScrollTrigger `onRefresh` has rebuilt bounds (tracks added before `set()` start at `[0, 0]`). If GSAP already advanced scrub progress during that first refresh, Timeline catch-up runs right after `eRefresh` so skipped leave/enter stay consistent on history restore.

---

## See also

- [Plugins index](../plugins.md)  
- [Core — Event](../core.md) · [Recipes](../recipes.md)