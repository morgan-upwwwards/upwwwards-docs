# Scripts

Standalone **utilities** under `upwwwards/scripts`. They do **not** need `Up.set` or the Vite plugin — usable with or without the upwwwards runtime.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import { Colors, Cookies, Logs, Wait, Arrays } from 'upwwwards/scripts'
```

Every script is exported from the barrel `upwwwards/scripts`. Most are static helper classes; many also ship a TypeScript **namespace** of types (e.g. `Colors.RGB`, `Arrays.Join`).

### Catalog

| Script | Import | Role | Doc |
| --- | --- | --- | --- |
| [Maths](scripts/maths.md) | `Maths` | Math helpers, angles, clamps, type-level numbers | Runtime + types |
| [Strings](scripts/strings.md) | `Strings` | String helpers, case, canvas text metrics | Runtime + types |
| [Parser](scripts/parser.md) | `Parser` | Type tags, clone / compare, object merges (types) | Runtime + types |
| [Wait](scripts/wait.md) | `Wait` | rAF coalesce, debounce/throttle, sleep, static files | Mostly client |
| [Cookies](scripts/cookies.md) | `Cookies` | `document.cookie` get/set/destroy | Browser-only |
| [Trees](scripts/trees.md) | `Trees` | Interval tree (`Trees.Interval`) | Runtime |
| [Colors](scripts/colors.md) | `Colors` | Color convert (hex / RGB / HSL) | Runtime + types |
| [Is](scripts/is.md) | `Is` | Validators (email, tel, …) + visibility | Runtime + types |
| [Arrays](scripts/arrays.md) | `Arrays` | Array/tuple types + `CallStack` | Types + runtime |
| [Logs](scripts/logs.md) | `Logs` | Tagged console / throw / reject | Runtime |

### Browser vs. SSR

- `Cookies` throws without `document` — prefer the **`Up.Adapter` cookie helpers** on SSR paths.
- `Wait.frame` / `tick` / `staticFiles` expect a browser.

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### Deep imports

Only `Trees` and `Arrays` have deep paths (package exports). Every other script is **barrel-only**.

```ts
import Trees from 'upwwwards/scripts/Trees'
```

Full export map: [compatibility](compatibility.md#package-exports).

### Constructible helpers

`Trees` and `Arrays` also expose classes you instantiate:

- `new Trees.Interval()` — interval tree ([Trees](scripts/trees.md))
- `new Arrays.CallStack()` — ordered callback stack ([Arrays](scripts/arrays.md))

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

### Internal: `Hrt`

`Hrt` (heritage / merge stacks) lives under `src/scripts/Hrt.ts` and is used by the runtime (`App.HRT`). It is a **side-effect** build entry and is **not** part of the public `upwwwards/scripts` export catalog — **do not import it** from app code.

### Types and JSDoc

Deep type aliases and edge cases live in JSDoc on each class; the pages in this section cover the public surface used day-to-day.

---

## See also

- [Core](core.md) — runtime that uses several of these (`Wait.staticFiles` before `LOAD`, `Logs`, …)
- [Documentation index](index.md)
