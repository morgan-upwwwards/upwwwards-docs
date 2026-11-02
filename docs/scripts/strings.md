# Strings

String utilities at **runtime** and **type level** (case transforms, whitespace, kebab/camel helpers, template-safe `STRINGIFIABLE`, …).

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import { Strings } from 'upwwwards/scripts'

Strings.kebab('HelloWorld') // 'hello-world'
Strings.truncate('lorem ipsum dolor', 10, true) // 'lorem…'
```

### Runtime (static)

| API | Role |
| --- | --- |
| `capitalize(s, andLowerCase?)` | Capitalize (optionally lower the rest) |
| `kebab(s)` | To kebab-case |
| `truncate(s, n, wordBoundary?, ellipsis?)` | Truncate with optional word boundary |
| `simplify(s)` | Strip diacritics (`NFD`) |
| `escapeHTML(s)` | Escape `& < > " '` |
| `htmlToText(html)` | Strip tags to text |
| `getBiggestWord(s)` | Longest word |
| `randomKey(key?, guarantor?)` | Unique string key helper |

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### Markdown and whitespace

| API | Role |
| --- | --- |
| `strongMDToHTML` / `strongMDToText` | Lightweight `**strong**` markdown helpers |
| `transformWhiteSpaces(s, whiteSpace)` | Apply CSS `white-space` semantics |

### Text metrics (browser)

| API | Role |
| --- | --- |
| `measure(s, style)` | Canvas-backed text metrics for one string |
| `measures(style)` | Related ink / font metrics helpers |

These need a browser (they use a canvas context).

### Types

Namespace helpers such as `Strings.KebabCase`, `Includes`, `RemoveAtStart` / `RemoveAtEnd`, `STRINGIFIABLE`.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Type-level details and edge cases are documented in JSDoc on the `Strings` namespace.

---

## See also

- [Scripts index](../scripts.md)
