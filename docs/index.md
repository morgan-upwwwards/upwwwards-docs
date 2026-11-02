# Documentation

**upwwwards** gives **Nuxt 4** and **SvelteKit 2** apps one shared runtime: a page lifecycle (leave / enter / ready), a single frame loop, and opt-in plugins — all on `Up`.

The full documentation lives in this folder, plus the root [README](../README.md) and [`llms.txt`](../llms.txt). A hosted docs site and a changelog are planned.

**Recommended path:** [getting-started](getting-started.md) → [concepts](concepts.md) → [core](core.md). Skim concepts first if you want the mental model before wiring.

API pages (`core`, adapters, plugins, scripts, …) and [recipes](recipes.md) use three levels (markdown-safe markers — npm strips custom CSS colors):

| | Level | Meaning |
| --- | --- | --- |
| 🟢 | **Essentials** | Day-to-day use — start here |
| 🟡 | **Going further** | Optional power features — safe to skip |
| ⬛ | **Under the hood** | Internals / debug — safe to skip |

## I want to…

| Goal | Go to |
| --- | --- |
| Install and wire Nuxt or SvelteKit | [getting-started.md](getting-started.md) |
| Understand why it exists | [concepts.md](concepts.md) · [README](../README.md) |
| Learn `DEATH` / `LIFE` / `LOAD` and the core API | [core.md](core.md) |
| Copy a common pattern (Anime, Screen, fonts, modal) | [recipes.md](recipes.md) |
| Debug navigation / `ABORTED` / Nuxt middleware | [adapters.md](adapters.md) · [faq.md](faq.md) |
| Configure the Vite plugin (`up.event`, `up.thread`, …) | [initializers.md](initializers.md) |
| Enable Scroll, Anime, Synthra, … | [plugins.md](plugins.md) |
| Use cookies on SSR | [contexts.md](contexts.md) |
| Grab utilities (`Wait`, `Logs`, `Maths`, …) | [scripts.md](scripts.md) |
| Generate atomic CSS | [classter.md](classter.md) · [alias catalog](classter-aliases.md) |
| Patch a plugin / use deep imports | [core — Up.patch](core.md#uppatchpluginclass) · [compatibility — exports](compatibility.md#package-exports) |
| Patch / debug internals | [advanced/symbols.md](advanced/symbols.md) |
| Check host / peer versions / exports | [compatibility.md](compatibility.md) |
| Check the license | [advanced/license.md](advanced/license.md) |

## Reading paths

**Ship a feature this week**

1. [Getting started](getting-started.md)  
2. [Concepts](concepts.md)  
3. [Core](core.md) — `Event` + `App`  
4. [Recipes](recipes.md) and only the [plugins](plugins.md) you enable  

**Go deep**

[Initializers](initializers.md) → [adapters](adapters.md) → [symbols](advanced/symbols.md) → plugin pages  

**Unblock**

[FAQ](faq.md)

## Catalog

| Doc | Layer |
| --- | --- |
| [getting-started.md](getting-started.md) | Setup |
| [concepts.md](concepts.md) | Mental model |
| [core.md](core.md) | **core** |
| [recipes.md](recipes.md) | Patterns |
| [initializers.md](initializers.md) | **initializers** |
| [adapters.md](adapters.md) | **adapters** |
| [contexts.md](contexts.md) | **contexts** |
| [plugins.md](plugins.md) | **plugins** → [Observer](plugins/observer.md), [Screen](plugins/screen.md), [Transmitter](plugins/transmitter.md), [Scroll](plugins/scroll.md), [Anime](plugins/anime.md), [Puppeteer](plugins/puppeteer.md), [Synthra](plugins/synthra.md) |
| [scripts.md](scripts.md) | **scripts** → [Maths](scripts/maths.md), [Strings](scripts/strings.md), [Parser](scripts/parser.md), [Wait](scripts/wait.md), [Cookies](scripts/cookies.md), [Trees](scripts/trees.md), [Colors](scripts/colors.md), [Is](scripts/is.md), [Arrays](scripts/arrays.md), [Logs](scripts/logs.md) |
| [classter.md](classter.md) | **classter** · [aliases](classter-aliases.md) |
| [faq.md](faq.md) | Troubleshooting |
| [compatibility.md](compatibility.md) | Versions / npm surface |
| [advanced/symbols.md](advanced/symbols.md) | **symbols** |
| [advanced/license.md](advanced/license.md) | License |

## Mental model

```
Vite initializer (adapter + plugins + classter)
        ↓
import Up from 'upwwwards'
        ↓
Up.set(adapterConfig)          // once, browser
        ↓
Up.Adapter  → BEFORE / ON / AFTER / ABORTED
Up.Event    → DEATH (leave) / LIFE (enter) / LOAD (ready)
Up.Thread   → frame scheduling
Up.*Plugin  → optional instruments
```

AI agents: start from [`llms.txt`](../llms.txt).
