# Is

Input validators and a **viewport visibility** helper.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import { Is } from 'upwwwards/scripts'

Is.email('jane@doe.com')
Is.tel('+1 (555) 123-4567')
Is.visible(element) // Promise<boolean> — in viewport?
```

| API | Role |
| --- | --- |
| `email` / `tel` / `name` / `username` | Format checks |
| `visible(el, delay?)` | Viewport visibility (async) |

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

| API | Role |
| --- | --- |
| `date(year?, month?, day?, minYear?, maxYear?)` | Date parts |
| `time(hour, minutes)` | Clock time |
| `birth('YYYY-MM-DD')` | Birth-date helper |

`Wait.staticFiles({ viewportOnly: true })` uses `Is.visible` for skip decisions.

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- Optional global overrides at runtime (types still model defaults), e.g. `Is.emailRegex = …`.  
- Compile-time mirrors exist as types (`Is.Email<'…'>`, `Is.Tel<'…'>`, …).

---

## See also

- [Scripts index](../scripts.md)  
- [Wait](wait.md)  
