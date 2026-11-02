# Classter

**Optional** atomic CSS generator, for teams that want atomic class tokens instead of (or alongside) Tailwind. It is **not required** for upwwwards core. It scans templates for class tokens and writes rules into the virtual stylesheet **`classter.css`**.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### Enable

```ts
import initializer from 'upwwwards/initializers/vite'
import Vue from 'upwwwards/classter/Vue'       // Nuxt
// import Svelte from 'upwwwards/classter/Svelte' // SvelteKit

initializer({
  adapter: 'nuxt',
  classter: { Classter: Vue }
})
```

Import once in the app:

```ts
import 'classter.css'
```

### First tokens

```
alias:value
alias:[value]
```

```html
<div class="pa:1rem ma:[0_auto] ba-co:[#111] co:[#fff]" />
```

- **alias** — shortcut for a CSS property (`pa` → `padding`, `ba-co` → `background-color`).  
- **value** — plain token, or `[…]` where `_` means space (`ma:[0_auto]` → `margin: 0 auto`).  

Sample aliases: `di` display · `pa` / `ma` padding / margin · `wi` / `he` width / height · `co` / `ba-co` color / background-color.

Full table: [classter-aliases.md](classter-aliases.md).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### Pseudo segment

```
alias:value:pseudo
```

**`:before` or `:after` only** (`co:[#fff]:before` → `::before`). Other pseudo-classes (`:hover`, `:focus`, …) are **not** supported as the third segment.

### Alias shortcut rule

Default aliases follow: **first two letters of each hyphen-separated word** (`ba-co` → `background-color`). Collisions keep the last word in full (`bo-collapse`, `translate`, …).

```ts
import Vue from 'upwwwards/classter/Vue'

Vue.addAlias({ 'my-pa': 'padding' }) // skips keys that already exist (warns)
```

Also via initializer `classter.alias`.

### Identifiers

By default Classter reads `class="…"`. Extend via `classter.identifiers` (also `Classter.addClassIdentifiers` / `addAttributesIdentifiers`). Names are stored in **lowercase and kebab-case**.

| Key | Role |
| --- | --- |
| `identifiers.class` | Extra prop / attribute names that hold class tokens |
| `identifiers.attributes` | Prop names whose **object** values are walked for nested class keys |

```ts
initializer({
  adapter: 'nuxt',
  classter: {
    Classter: Vue,
    identifiers: {
      class: ['innerClass'],
      attributes: ['attrs']
    }
  }
})
```

Plain HTML scan only reads double-quoted `class="…"` (custom class identifiers are ignored there).

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

| Option | Role |
| --- | --- |
| `off` | Disable Classter — do **not** import `classter.css` |
| `silent` | Quiets some unknown-alias / non-static warnings |

- Live `classter.css` + HMR when templates change.  
- At-rule aliases (`@me` → `@media`, …) exist in the shared table but are not typical `class` tokens — see [classter-aliases](classter-aliases.md).

---

## See also

- [Classter aliases](classter-aliases.md)  
- [Initializers](initializers.md)  
- [Getting started](getting-started.md)  
