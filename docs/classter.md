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
- **value** — plain token (no spaces / `_`), or `[…]` where underscores are expanded (see [Underscores](#underscores)).

Sample aliases: `di` display · `pa` / `ma` padding / margin · `wi` / `he` width / height · `co` / `ba-co` color / background-color.

Full default alias catalog (every entry from `props.ts`): **[classter-aliases.md](classter-aliases.md)**.

### Underscores

Inside **`[…]` values / selectors** and inside **pseudo `(…)` args**, runs of `_` are reduced:

| Token fragment | Result |
| --- | --- |
| `_` | one space |
| `__` | one literal `_` |
| `___` | `__` |
| … | each run of length *n* → *n − 1* underscores (`n = 1` → space) |

Examples: `ma:[0_auto]` → `margin:0 auto` · `ma:[0__auto]` → `margin:0_auto` · `pa:1rem:is(.my__class)` → `:is(.my_class)`.

**Not** applied on bare (non-bracket) selector segments such as `>ul+.my_class` — underscores there stay as `_`.

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### Selector + pseudo segments

```
alias:value
alias:value:pseudo
alias:value>ul+.x:pseudo
alias:value[selector]:pseudo
```

Emitted rule shape: `.TOKEN` + **selector suffix** + `{declaration}` (the full token string is the class name, escaped).

**Selector** *(optional)* — either:

- a bare chain starting with a combinator `>` / `+` / `~` / `|` (no spaces; `_` kept literal), e.g. `pa:1rem>ul+.my_class`, or
- a `[…]` fragment (underscores expanded), e.g. `pa:1rem[>ul_.my__class]` → `>ul .my_class`.

**Pseudo** *(optional)* — any pseudo-class / pseudo-element from [classter-aliases](classter-aliases.md). Token suffix uses one colon (`:before`, `:hover`, `:nth-child(…)`); the table maps to the real CSS (`::before`, `:hover`, …).

Functional args `(…)` always run underscore reduction: `pa:1rem:nth-child(2n_+_1)` → `:nth-child(2n + 1)`. For a class name that contains `_`, double it: `:is(.my__class)`.

```html
<div class="pa:1rem:hover co:[#fff]:before" />
<div class="ma:[5rem_8rem]>ul+.item:is(strong,mark)" />
<div class="pa:1rem[>ul_.my__class]:has([data-x])" />
```

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

Classter only keeps tokens it can resolve **statically**. Identifiers and member access (`foo`, `obj.x`) are skipped (no warning).

| Host | Supported shapes (examples) |
| --- | --- |
| **Vue** | `class="…"`, `:class="'…'"`, `` :class="`…${…}`" `` (nested templates OK), arrays, `{ 'pa:1rem': cond }`, ternaries / `&&` `\|\|` `??`, string `+` (literals / templates / chains), sequences, tagged templates (quasis only), `v-bind="{ class: … }"`; TS `as` / `satisfies` / non-null `!` stripped before Meriyah |
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
- Token regex: `alias` + plain or `[value]` + optional bare combinator chain or `[selector]` + optional `:pseudo(args)?`. Underscore reduction runs on bracket bodies and on pseudo `(…)` only.
- **Vue:** directive expressions go through Meriyah (as `(expr)` so `{ class: … }` is an object, not a block), then the shared ESTree walker. Type assertions / `satisfies` / non-null `!` are stripped first; parse failures warn and skip instead of breaking the Vite transform. Call / member / optional-chain expressions are ignored (runtime).
- **Svelte:** walks the compiler AST directly (no string re-parse for `class={…}`).
- **`runProperties`:** `checkKey` true → keys are class tokens (Identifier + string Literal); `checkKey` false → follow `CLASS_IDENTIFIERS` values and recurse into `ATTRIBUTES_IDENTIFIERS` object bags (object literals only).
- At-rule aliases (`@me` → `@media`, …) exist in the shared table but are not typical `class` tokens — see [classter-aliases](classter-aliases.md).

---

## See also

- [Classter aliases](classter-aliases.md) — complete default alias table  
- [Initializers](initializers.md)  
- [Getting started](getting-started.md)