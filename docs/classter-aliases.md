# Classter aliases

Full default alias table shipped with Classter. Shortcut rule: first two letters of each hyphen-separated word (`ba-co` → `background-color`); collisions keep the last word in full (`bo-collapse`, `translate`, …).

Token syntax and setup: [classter.md](classter.md).

**How to use this page**

- Day-to-day class tokens use the **Properties** and **Pseudo-elements** sections (`pa:1rem`, `co:[#fff]:before`).
- **At-rules** (`@me` → `@media`, …) are part of the shared alias table for completeness; they are not typical `class="…"` tokens. Prefer property aliases in templates.
- Properties are grouped by the first letter of the alias. Use editor search for a CSS property name (e.g. `justify-content`).

## At-rules

| Alias | CSS |
| --- | --- |
| `@ch` | `@charset` |
| `@co` | `@container` |
| `@co-st` | `@counter-style` |
| `@fo-fa` | `@font-face` |
| `@fo-pa-va` | `@font-palette-values` |
| `@im` | `@import` |
| `@ke` | `@keyframes` |
| `@la` | `@layer` |
| `@me` | `@media` |
| `@na` | `@namespace` |
| `@pa` | `@page` |
| `@pr` | `@property` |
| `@sc` | `@scope` |
| `@st-st` | `@starting-style` |
| `@su` | `@supports` |

## Pseudo-elements

| Alias | CSS |
| --- | --- |
| `:before` | `::before` |
| `:after` | `::after` |

## Properties

### A

| Alias | CSS |
| --- | --- |
| `ac-co` | `accent-color` |
| `al-co` | `align-content` |
| `al-it` | `align-items` |
| `al-se` | `align-self` |
| `al` | `all` |
| `an` | `animation` |
| `an-de` | `animation-delay` |
| `an-di` | `animation-direction` |
| `an-du` | `animation-duration` |
| `an-fi-mo` | `animation-fill-mode` |
| `an-it-co` | `animation-iteration-count` |
| `an-na` | `animation-name` |
| `an-pl-st` | `animation-play-state` |
| `an-ti-fu` | `animation-timing-function` |
| `as-ra` | `aspect-ratio` |

### B

| Alias | CSS |
| --- | --- |
| `ba-fi` | `backdrop-filter` |
| `ba-vi` | `backface-visibility` |
| `ba` | `background` |
| `ba-at` | `background-attachment` |
| `ba-bl-mo` | `background-blend-mode` |
| `ba-cl` | `background-clip` |
| `ba-co` | `background-color` |
| `ba-im` | `background-image` |
| `ba-or` | `background-origin` |
| `ba-po` | `background-position` |
| `ba-po-x` | `background-position-x` |
| `ba-po-y` | `background-position-y` |
| `ba-re` | `background-repeat` |
| `ba-si` | `background-size` |
| `bl-si` | `block-size` |
| `bo` | `border` |
| `bo-bl` | `border-block` |
| `bo-bl-co` | `border-block-color` |
| `bo-bl-en` | `border-block-end` |
| `bo-bl-en-co` | `border-block-end-color` |
| `bo-bl-en-st` | `border-block-end-style` |
| `bo-bl-en-wi` | `border-block-end-width` |
| `bo-bl-start` | `border-block-start` |
| `bo-bl-st-co` | `border-block-start-color` |
| `bo-bl-st-st` | `border-block-start-style` |
| `bo-bl-st-wi` | `border-block-start-width` |
| `bo-bl-st` | `border-block-style` |
| `bo-bl-wi` | `border-block-width` |
| `bo-bo` | `border-bottom` |
| `bo-bo-co` | `border-bottom-color` |
| `bo-bo-le-ra` | `border-bottom-left-radius` |
| `bo-bo-ri-ra` | `border-bottom-right-radius` |
| `bo-bo-st` | `border-bottom-style` |
| `bo-bo-wi` | `border-bottom-width` |
| `bo-collapse` | `border-collapse` |
| `bo-co` | `border-color` |
| `bo-en-en-ra` | `border-end-end-radius` |
| `bo-en-st-ra` | `border-end-start-radius` |
| `bo-im` | `border-image` |
| `bo-im-ou` | `border-image-outset` |
| `bo-im-re` | `border-image-repeat` |
| `bo-im-sl` | `border-image-slice` |
| `bo-im-so` | `border-image-source` |
| `bo-im-wi` | `border-image-width` |
| `bo-in` | `border-inline` |
| `bo-in-co` | `border-inline-color` |
| `bo-in-en` | `border-inline-end` |
| `bo-in-en-co` | `border-inline-end-color` |
| `bo-in-en-st` | `border-inline-end-style` |
| `bo-in-en-wi` | `border-inline-end-width` |
| `bo-in-start` | `border-inline-start` |
| `bo-in-st-co` | `border-inline-start-color` |
| `bo-in-st-st` | `border-inline-start-style` |
| `bo-in-st-wi` | `border-inline-start-width` |
| `bo-in-st` | `border-inline-style` |
| `bo-in-wi` | `border-inline-width` |
| `bo-le` | `border-left` |
| `bo-le-co` | `border-left-color` |
| `bo-le-st` | `border-left-style` |
| `bo-le-wi` | `border-left-width` |
| `bo-ra` | `border-radius` |
| `bo-ri` | `border-right` |
| `bo-ri-co` | `border-right-color` |
| `bo-ri-st` | `border-right-style` |
| `bo-ri-wi` | `border-right-width` |
| `bo-sp` | `border-spacing` |
| `bo-st-en-ra` | `border-start-end-radius` |
| `bo-st-st-ra` | `border-start-start-radius` |
| `bo-st` | `border-style` |
| `bo-to` | `border-top` |
| `bo-to-co` | `border-top-color` |
| `bo-to-le-ra` | `border-top-left-radius` |
| `bo-to-ri-ra` | `border-top-right-radius` |
| `bo-to-st` | `border-top-style` |
| `bo-to-wi` | `border-top-width` |
| `bo-wi` | `border-width` |
| `bottom` | `bottom` |
| `bo-de-br` | `box-decoration-break` |
| `bo-re` | `box-reflect` |
| `bo-sh` | `box-shadow` |
| `bo-si` | `box-sizing` |
| `br-af` | `break-after` |
| `br-be` | `break-before` |
| `br-in` | `break-inside` |

### C

| Alias | CSS |
| --- | --- |
| `ca-si` | `caption-side` |
| `ca-co` | `caret-color` |
| `cl` | `clear` |
| `clip` | `clip` |
| `cl-pa` | `clip-path` |
| `co` | `color` |
| `co-sc` | `color-scheme` |
| `co-co` | `column-count` |
| `co-fi` | `column-fill` |
| `co-ga` | `column-gap` |
| `co-ru` | `column-rule` |
| `co-ru-co` | `column-rule-color` |
| `co-ru-st` | `column-rule-style` |
| `co-ru-wi` | `column-rule-width` |
| `co-sp` | `column-span` |
| `co-wi` | `column-width` |
| `columns` | `columns` |
| `content` | `content` |
| `co-in` | `counter-increment` |
| `co-re` | `counter-reset` |
| `co-se` | `counter-set` |
| `cu` | `cursor` |

### D

| Alias | CSS |
| --- | --- |
| `direction` | `direction` |
| `di` | `display` |

### E

| Alias | CSS |
| --- | --- |
| `em-ce` | `empty-cells` |

### F

| Alias | CSS |
| --- | --- |
| `fi` | `filter` |
| `fl` | `flex` |
| `fl-ba` | `flex-basis` |
| `fl-di` | `flex-direction` |
| `fl-fl` | `flex-flow` |
| `fl-gr` | `flex-grow` |
| `fl-sh` | `flex-shrink` |
| `fl-wr` | `flex-wrap` |
| `float` | `float` |
| `fo` | `font` |
| `fo-fa` | `font-family` |
| `fo-fe-se` | `font-feature-settings` |
| `fo-ke` | `font-kerning` |
| `fo-si` | `font-size` |
| `fo-si-ad` | `font-size-adjust` |
| `fo-stretch` | `font-stretch` |
| `fo-st` | `font-style` |
| `fo-va` | `font-variant` |
| `fo-va-ca` | `font-variant-caps` |
| `fo-we` | `font-weight` |

### G

| Alias | CSS |
| --- | --- |
| `ga` | `gap` |
| `gr` | `grid` |
| `gr-ar` | `grid-area` |
| `gr-au-co` | `grid-auto-columns` |
| `gr-au-fl` | `grid-auto-flow` |
| `gr-au-ro` | `grid-auto-rows` |
| `gr-co` | `grid-column` |
| `gr-co-en` | `grid-column-end` |
| `gr-co-st` | `grid-column-start` |
| `gr-ro` | `grid-row` |
| `gr-ro-en` | `grid-row-end` |
| `gr-ro-st` | `grid-row-start` |
| `gr-te` | `grid-template` |
| `gr-te-ar` | `grid-template-areas` |
| `gr-te-co` | `grid-template-columns` |
| `gr-te-ro` | `grid-template-rows` |

### H

| Alias | CSS |
| --- | --- |
| `ha-pu` | `hanging-punctuation` |
| `he` | `height` |
| `hy` | `hyphens` |
| `hy-ch` | `hyphenate-character` |

### I

| Alias | CSS |
| --- | --- |
| `im-re` | `image-rendering` |
| `in-le` | `initial-letter` |
| `in-si` | `inline-size` |
| `in` | `inset` |
| `in-bl` | `inset-block` |
| `in-bl-en` | `inset-block-end` |
| `in-bl-st` | `inset-block-start` |
| `in-in` | `inset-inline` |
| `in-in-en` | `inset-inline-end` |
| `in-in-st` | `inset-inline-start` |
| `is` | `isolation` |

### J

| Alias | CSS |
| --- | --- |
| `ju-co` | `justify-content` |
| `ju-it` | `justify-items` |
| `ju-se` | `justify-self` |

### L

| Alias | CSS |
| --- | --- |
| `left` | `left` |
| `le-sp` | `letter-spacing` |
| `li-he` | `line-height` |
| `li-st` | `list-style` |
| `li-st-im` | `list-style-image` |
| `li-st-po` | `list-style-position` |
| `li-st-ty` | `list-style-type` |

### M

| Alias | CSS |
| --- | --- |
| `ma` | `margin` |
| `ma-bl` | `margin-block` |
| `ma-bl-en` | `margin-block-end` |
| `ma-bl-st` | `margin-block-start` |
| `ma-bo` | `margin-bottom` |
| `ma-in` | `margin-inline` |
| `ma-in-en` | `margin-inline-end` |
| `ma-in-st` | `margin-inline-start` |
| `ma-le` | `margin-left` |
| `ma-ri` | `margin-right` |
| `ma-to` | `margin-top` |
| `marker` | `marker` |
| `ma-en` | `marker-end` |
| `ma-mi` | `marker-mid` |
| `ma-st` | `marker-start` |
| `mask` | `mask` |
| `ma-cl` | `mask-clip` |
| `ma-co` | `mask-composite` |
| `ma-im` | `mask-image` |
| `ma-mo` | `mask-mode` |
| `ma-or` | `mask-origin` |
| `ma-po` | `mask-position` |
| `ma-re` | `mask-repeat` |
| `ma-si` | `mask-size` |
| `ma-ty` | `mask-type` |
| `ma-he` | `max-height` |
| `ma-wi` | `max-width` |
| `ma-bl-si` | `max-block-size` |
| `ma-in-si` | `max-inline-size` |
| `mi-bl-si` | `min-block-size` |
| `mi-in-si` | `min-inline-size` |
| `mi-he` | `min-height` |
| `mi-wi` | `min-width` |
| `mi-bl-mo` | `mix-blend-mode` |

### O

| Alias | CSS |
| --- | --- |
| `ob-fi` | `object-fit` |
| `ob-po` | `object-position` |
| `of` | `offset` |
| `of-an` | `offset-anchor` |
| `of-di` | `offset-distance` |
| `of-pa` | `offset-path` |
| `of-po` | `offset-position` |
| `of-ro` | `offset-rotate` |
| `op` | `opacity` |
| `or` | `order` |
| `orphans` | `orphans` |
| `ou` | `outline` |
| `ou-co` | `outline-color` |
| `ou-of` | `outline-offset` |
| `ou-st` | `outline-style` |
| `ou-wi` | `outline-width` |
| `ov` | `overflow` |
| `ov-an` | `overflow-anchor` |
| `ov-wr` | `overflow-wrap` |
| `ov-x` | `overflow-x` |
| `ov-y` | `overflow-y` |
| `ov-be` | `overscroll-behavior` |
| `ov-be-bl` | `overscroll-behavior-block` |
| `ov-be-in` | `overscroll-behavior-inline` |
| `ov-be-x` | `overscroll-behavior-x` |
| `ov-be-y` | `overscroll-behavior-y` |

### P

| Alias | CSS |
| --- | --- |
| `pa` | `padding` |
| `pa-bl` | `padding-block` |
| `pa-bl-en` | `padding-block-end` |
| `pa-bl-st` | `padding-block-start` |
| `pa-bo` | `padding-bottom` |
| `pa-in` | `padding-inline` |
| `pa-in-en` | `padding-inline-end` |
| `pa-in-st` | `padding-inline-start` |
| `pa-le` | `padding-left` |
| `pa-ri` | `padding-right` |
| `pa-to` | `padding-top` |
| `pa-br-af` | `page-break-after` |
| `pa-br-be` | `page-break-before` |
| `pa-br-in` | `page-break-inside` |
| `pa-or` | `paint-order` |
| `pe` | `perspective` |
| `pe-or` | `perspective-origin` |
| `pl-co` | `place-content` |
| `pl-it` | `place-items` |
| `pl-se` | `place-self` |
| `po-ev` | `pointer-events` |
| `po` | `position` |

### Q

| Alias | CSS |
| --- | --- |
| `qu` | `quotes` |

### R

| Alias | CSS |
| --- | --- |
| `re` | `resize` |
| `right` | `right` |
| `ro` | `rotate` |
| `ro-ga` | `row-gap` |

### S

| Alias | CSS |
| --- | --- |
| `sc` | `scale` |
| `sc-be` | `scroll-behavior` |
| `sc-ma` | `scroll-margin` |
| `sc-ma-bl` | `scroll-margin-block` |
| `sc-ma-bl-en` | `scroll-margin-block-end` |
| `sc-ma-bl-st` | `scroll-margin-block-start` |
| `sc-ma-bo` | `scroll-margin-bottom` |
| `sc-ma-in` | `scroll-margin-inline` |
| `sc-ma-in-en` | `scroll-margin-inline-end` |
| `sc-ma-in-st` | `scroll-margin-inline-start` |
| `sc-ma-le` | `scroll-margin-left` |
| `sc-ma-ri` | `scroll-margin-right` |
| `sc-ma-to` | `scroll-margin-top` |
| `sc-pa` | `scroll-padding` |
| `sc-pa-bl` | `scroll-padding-block` |
| `sc-pa-bl-en` | `scroll-padding-block-end` |
| `sc-pa-bl-st` | `scroll-padding-block-start` |
| `sc-pa-bo` | `scroll-padding-bottom` |
| `sc-pa-in` | `scroll-padding-inline` |
| `sc-pa-in-en` | `scroll-padding-inline-end` |
| `sc-pa-in-st` | `scroll-padding-inline-start` |
| `sc-pa-le` | `scroll-padding-left` |
| `sc-pa-ri` | `scroll-padding-right` |
| `sc-pa-to` | `scroll-padding-top` |
| `sc-sn-al` | `scroll-snap-align` |
| `sc-sn-st` | `scroll-snap-stop` |
| `sc-sn-ty` | `scroll-snap-type` |
| `sc-co` | `scrollbar-color` |
| `sh-ou` | `shape-outside` |

### T

| Alias | CSS |
| --- | --- |
| `ta-si` | `tab-size` |
| `ta-la` | `table-layout` |
| `te-al` | `text-align` |
| `te-al-la` | `text-align-last` |
| `te-de` | `text-decoration` |
| `te-de-co` | `text-decoration-color` |
| `te-de-li` | `text-decoration-line` |
| `te-de-st` | `text-decoration-style` |
| `te-de-th` | `text-decoration-thickness` |
| `te-em` | `text-emphasis` |
| `te-em-co` | `text-emphasis-color` |
| `te-em-po` | `text-emphasis-position` |
| `te-em-st` | `text-emphasis-style` |
| `te-in` | `text-indent` |
| `te-ju` | `text-justify` |
| `te-or` | `text-orientation` |
| `te-ov` | `text-overflow` |
| `te-sh` | `text-shadow` |
| `te-tr` | `text-transform` |
| `te-un-of` | `text-underline-offset` |
| `te-un-po` | `text-underline-position` |
| `top` | `top` |
| `tr` | `transform` |
| `tr-or` | `transform-origin` |
| `tr-st` | `transform-style` |
| `transition` | `transition` |
| `tr-de` | `transition-delay` |
| `tr-du` | `transition-duration` |
| `tr-pr` | `transition-property` |
| `tr-ti-fu` | `transition-timing-function` |
| `translate` | `translate` |

### U

| Alias | CSS |
| --- | --- |
| `un-bi` | `unicode-bidi` |
| `us-se` | `user-select` |

### V

| Alias | CSS |
| --- | --- |
| `ve-al` | `vertical-align` |
| `vi` | `visibility` |

### W

| Alias | CSS |
| --- | --- |
| `wh-sp` | `white-space` |
| `widows` | `widows` |
| `wi` | `width` |
| `wo-br` | `word-break` |
| `wo-sp` | `word-spacing` |
| `wo-wr` | `word-wrap` |
| `wr-mo` | `writing-mode` |

### Z

| Alias | CSS |
| --- | --- |
| `z-in` | `z-index` |
| `zo` | `zoom` |

