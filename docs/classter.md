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
- **selector** *(optional)* — `_` / `>` / `+` / `~`, or `[…]`, appended after the class (`pa:1rem_[li]`, `ma:[5rem_8rem][>ul+.x>]:is(strong,mark)`).

Sample aliases: `di` display · `pa` / `ma` padding / margin · `wi` / `he` width / height · `co` / `ba-co` color / background-color.

Full default alias catalog (every entry from `props.ts`): **[classter-aliases.md](classter-aliases.md)**.

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### Selector + pseudo segments

```
alias:value
alias:value:pseudo
alias:value[selector]:pseudo
alias:value>:pseudo
```

Optional **selector**: a single combinator `_` (descendant space), `>`, `+`, `~`, or a `[…]` fragment ( `_` → space inside). Then optional **pseudo** from [classter-aliases](classter-aliases.md) (`co:[#fff]:before` → `::before`, `pa:1rem:hover` → `:hover`). Functional args use `_` for spaces (`pa:1rem:nth-child(2n_+_1)` → `:nth-child(2n + 1)`).

Example: `ma:[5rem_8rem][>ul+.class>]:is(strong,mark:not(.red))` →  
`.ma\:\[5rem_8rem\]\[\>ul\+\.class\>\]\:is\(strong\,mark\:not\(\.red\)\)>ul+.class>:is(strong,mark:not(.red)){margin:5rem 8rem}`.

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
| `identifiers.attributes` | Prop names whose **object** values are walked for nested `class` keys (and nested attribute bags) |

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

Examples with `attributes: ['attrs']` (or `propAttrs`, …):

```html
<!-- Vue -->
<div v-bind="{ class: 'pa:1rem' }" />
<Comp :attrs="{ class: 'pa:1rem' }" />
<div v-bind="{ attrs: { class: 'pa:1rem' } }" />

<!-- Svelte -->
<div {...{ class: 'pa:1rem' }}></div>
<Comp propAttrs={{ class: 'pa:1rem' }}></Comp>
```

Plain HTML scan only reads double-quoted `class="…"` (custom class identifiers are ignored there).

### Binding forms (static analysis)

Classter only keeps tokens it can resolve **statically**. Identifiers and member access (`foo`, `obj.x`) are skipped.

| Host | Supported shapes (examples) |
| --- | --- |
| **Vue** | `class="…"`, `:class="'…'"`, `` :class="`…${…}`" `` (nested templates OK), arrays, `{ 'pa:1rem': cond }`, ternaries / logical `and`/`or`, `'a' + 'b'`, `v-bind="{ class: … }"` |
| **Svelte** | `class="…"`, `class={…}` (same expression shapes via the Svelte AST), `class="text {expr}"`, spreads `{...{ class: … }}`, attribute bags; `class:name={…}` pushes `name` only if it is already a Classter token |

**Quotes in Vue templates:** keep the attribute in double quotes and use single quotes inside objects, e.g. `:class="[{ 'pa:1rem': true }]"`. Nested double quotes break the HTML attribute.

**Svelte nuance:** in `class="x {y}"`, `{y}` is string-like — object **keys** inside that mustache are ignored. Use `class={{ 'pa:1rem': true }}` (or an array) when the object itself is the class value.

Sample aliases: use `di` for `display` (not `d`), `pa` / `ma`, `wi` / `he`, `co` / `ba-co`.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

| Option | Role |
| --- | --- |
| `off` | Disable Classter — do **not** import `classter.css` |
| `silent` | Quiets some unknown-alias / non-static warnings |

- Live `classter.css` + HMR when templates change.  
- **Vue:** directive expressions go through Meriyah (as `(expr)` so `{ class: … }` is an object, not a block), then the shared ESTree walker. Type assertions (`as any`, …) are stripped first; parse failures warn and skip instead of breaking the Vite transform. Call / member expressions are ignored (runtime).  
- **Svelte:** walks the compiler AST directly (no string re-parse for `class={…}`).  
- **`runProperties`:** `checkKey` true → keys are class tokens (Identifier + string Literal); `checkKey` false → follow `CLASS_IDENTIFIERS` values and recurse into `ATTRIBUTES_IDENTIFIERS` object bags.  
- At-rule aliases (`@me` → `@media`, …) exist in the shared table but are not typical `class` tokens — see [classter-aliases](classter-aliases.md).

Regression harnesses (dev): `upwwwards/scripts/classter-vue-cases.mjs` and `classter-svelte-cases.mjs`.

---

## See also

- [Classter aliases](classter-aliases.md) — complete default alias table  
- [Initializers](initializers.md)  
- [Getting started](getting-started.md)