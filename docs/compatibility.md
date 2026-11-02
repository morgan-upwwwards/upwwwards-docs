# Compatibility

This page is the support matrix for **upwwwards 0.1.0-beta.1**. Track versions via the published package on npm and git tags; a changelog is planned.

## Hosts (required)

| Host | Adapter | Notes |
| --- | --- | --- |
| **Nuxt 4** | `adapter: 'nuxt'` | Peer `nuxt` ^4 + `vue` ^3 |
| **SvelteKit 2** | `adapter: 'svelte'` | Peers `@sveltejs/kit` ^2 + `svelte` ^5 |

No official adapters for other frameworks.

## Tooling

| Package | Peer range | Role |
| --- | --- | --- |
| `vite` | ^7 | Initializer / Classter |
| `h3` | ^1 | Optional; Nuxt SSR [contexts](contexts.md) |

## Plugin peers (optional)

| Peer | Range | Plugin |
| --- | --- | --- |
| `gsap` | ^3 | Anime |
| `lenis` | ^1 | Scroll |
| `three` | ^0 | Synthra |

All listed as **optional** peers in `package.json`. Install only what you enable, with any package manager (for example `npm install gsap`; `pnpm add`, `yarn add`, and `bun add` work too).

## Package exports

| Export | Role |
| --- | --- |
| `upwwwards` | Default `Up` |
| `upwwwards/initializers/vite` | Vite plugin |
| `upwwwards/adapters` | Adapter barrel |
| `upwwwards/adapters/Nuxt` · `…/Svelte` | Host adapter modules / types |
| `upwwwards/contexts/nuxt` · `…/sveltekit` | SSR context |
| `upwwwards/plugins` | Plugin barrel (all built-ins) |
| `upwwwards/plugins/{Transmitter,Scroll,Anime,Puppeteer,Synthra}` | Deep plugin imports (for `Up.patch`, types) |
| `upwwwards/scripts` | Script utilities barrel |
| `upwwwards/scripts/{Trees,Arrays}` | Deep script imports (other scripts: barrel only) |
| `upwwwards/classter/{Vue,Svelte}` | Classter host scanners |
| `upwwwards/symbols` | Advanced lifecycle symbols |

**Not deep-exported:** Observer, Screen (use `upwwwards/plugins` or `Up.*` after enable). Other scripts (`Maths`, `Wait`, …) → `upwwwards/scripts` only.

## Documentation surface

| Surface | What you get |
| --- | --- |
| npm package page | Primarily the root **README** |
| Git repo `docs/` | Full documentation (this folder) |
| [`llms.txt`](../llms.txt) | Agent-oriented map |

A hosted docs site is planned.

## Tests, performance, cookies (brief)

- **Tests:** prefer e2e against a real Nuxt/SvelteKit app. Avoid calling `Up.destroy()` in HMR unless you accept an irreversible shutdown — [core — Under the hood](core.md#runtime-boot).  
- **Performance:** tune `up.thread.flowBudget` / `foreverBudget`; prefer `TICK` / Event over ad-hoc forever tasks; use Thread generators to spread heavy work — [core — Thread](core.md#upthread--when-tick-is-not-enough).  
- **Cookies:** Adapter / `Cookies` defaults favor `Secure`, `SameSite=Strict`, `path=/`. Screen persists format in `up::plugins::screen::format` (~1 year). Use SSR [contexts](contexts.md) for server cookies; `httpOnly` is server-only on Adapter.

## See also

- [Getting started](getting-started.md) · [FAQ](faq.md) · [License](advanced/license.md)  
