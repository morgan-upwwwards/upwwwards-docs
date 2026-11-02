# Concepts

A short mental model for newcomers. Setup first? → [getting-started](getting-started.md).

## The problem it solves

In a typical Nuxt or SvelteKit app with rich client behavior, every feature ends up with its own clock: router hooks here, Lenis there, GSAP contexts elsewhere, a Three.js loop on the side. Each invents “the page changed” and its own cleanup — leaks and ordering bugs follow.

**upwwwards gives every feature the same three reference points:**

1. **Navigation** — leave / enter / ready (`DEATH` / `LIFE` / `LOAD`)  
2. **Time** — one shared `requestAnimationFrame` loop  
3. **Readiness** — one place to wait (fonts, images, data) before `LOAD`

It does not replace Nuxt or SvelteKit. You still write a normal app; features subscribe to the same cues.

## Page cycle

**`DEATH` = leave · `LIFE` = enter · `LOAD` = ready.**

Successful client nav: `BEFORE_NAVIGATE` → `ON_NAVIGATE` (+ `DEATH`) → `AFTER_NAVIGATE` (+ `LIFE` → `LOAD`).

First load / hard refresh: only `AFTER` → `LIFE` → `LOAD` (no `DEATH`). Failed nav after start → `ABORTED` (no `LIFE`/`LOAD` for that attempt).

Rule of thumb: **construct on `LIFE`, destroy on `DEATH`**.

Diagrams and Adapter phase sources: [core — Essentials](core.md#essentials) · [adapters](adapters.md) · [getting-started — First load](getting-started.md#first-load-note).

## Core modules (map)

| Module | Responsibility |
| --- | --- |
| `Up.Adapter` | Host bridge: navigation phases, refs, cookies |
| `Up.App` | Viewport metrics, cache, `addWait` before `LOAD` |
| `Up.Thread` | Frame scheduler (prefer `Event` `TICK` day-to-day) |
| `Up.Event` | Page lifecycle + DOM / frame events |

## Plugins & scripts

Plugins are **opt-in** in the Vite `plugins` option; lazy ones (Anime, Scroll, Synthra) load peers on first real use → [plugins](plugins.md).

`upwwwards/scripts` (`Wait`, `Logs`, `Maths`, …) are standalone — no `Up.set` required → [scripts](scripts.md).

## What upwwwards is not

- Not a page-transition library by itself (it coordinates *when* your transitions run)  
- Not a replacement for GSAP, Lenis, or Three.js  
- Not a multi-framework UI kit — official adapters are Nuxt 4 and SvelteKit 2  

## Next

- [Getting started](getting-started.md)  
- [Core](core.md) — start at **Essentials** · [Recipes](recipes.md) · [FAQ](faq.md)  
