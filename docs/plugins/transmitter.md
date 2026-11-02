# Transmitter

Typed **request / response channels** between parts of the app. Optional “channel open” state as an adapter ref.

| | |
| --- | --- |
| **Enable** | `plugins: ['Transmitter']` |
| **Access** | `Up.Transmitter` |
| **Peers** | none |

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

```ts
import Up from 'upwwwards'

Up.Transmitter.sub({
  channel: 'PING',
  receiver: (message: 'ping') => 'pong' as const
})

const answer = Up.Transmitter.broadcast('PING', 'ping') // 'pong'

Up.Transmitter.unsub({ channel: 'PING' })
```

- One receiver per channel; a new `sub` replaces the previous.  
- `broadcast` is **client-only**.  
- Keep the same patterns as Event: register a receiver, then broadcast.

Recipe: [recipes — Transmitter](../recipes.md#transmitter-channel).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### Channel open state

```ts
const $open = Up.Transmitter.getState('PING') // AdapterRef<boolean> — true while a receiver is registered
```

`unsub` closes the channel but keeps `getState` refs valid.

### Ticket options

| Option | Role |
| --- | --- |
| `force` | Allow repeated identical messages |
| `defaultReturn` | Value when a duplicate is ignored (`force` false) |
| `deadline` | Async only; `broadcast` rejects after ms (receiver keeps running) |

Without `force`, repeating the same message (`===`) can short-circuit to `defaultReturn`.

### Type augmentations

Augment `TransmitterChannels` in generated typings — [initializers](../initializers.md#generated-types-augmentations):

```ts
declare module 'upwwwards/plugins/Transmitter'
{
  import type { Transmitter } from 'upwwwards/plugins/Transmitter'

  export interface TransmitterChannels
  {
    PING: Transmitter.Config<false, 'ping', 'pong'>
    // LOAD_USER: Transmitter.Config<true, string, User>
  }
}
```

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- Sync receivers run immediately. Native `async` functions get a serialized async broadcaster (`deadline` supported). A sync function that *returns* a `Promise` still uses the sync path.  
- Extra ticket fields on `unsub` are ignored (only `channel` is read).  
- `Transmitter.Config<TAsync, TMessage, TReturnCases>` — `returnCases` may be a plain type or `Transmitter.Switch<…>` when the return depends on the message key.

---

## See also

- [Plugins index](../plugins.md)  
- [Initializers — type augmentations](../initializers.md#generated-types-augmentations)  
