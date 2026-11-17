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

const ticket = {
  channel: 'PING' as const,
  force: true as const,
  receiver: (message: 'ping'): 'pong' => 'pong'
}

Up.Transmitter.sub(ticket)

const answer = Up.Transmitter.broadcast('PING', 'ping') // 'pong'

Up.Transmitter.unsub(ticket) // only `channel` is read
```

- One receiver per channel; a new `sub` replaces the previous.  
- `sub` / `getState` / `isOnline` / `broadcast` / `unsub` work on **server and client**. On SSR, wire the [context](../contexts.md); channels stay **request-scoped** (`sub` and `broadcast` must share the same request).  
- Await async `broadcast` results before the SSR request ends.

Recipe: [recipes — Transmitter](../recipes.md#transmitter-channel).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

### Channel open state

```ts
const $open = Up.Transmitter.getState('PING') // AdapterRef<boolean> — true while a receiver is registered
const online = Up.Transmitter.isOnline('PING') // boolean snapshot — true while a receiver is registered
```

- `getState` — reactive ref; creates the channel closed if it does not exist yet.  
- `isOnline` — one-shot boolean; does **not** create the channel (missing → `false`).  
- `unsub` closes the channel but keeps `getState` refs valid.

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

- Sync receivers run immediately. Native `async` functions get a serialized async broadcaster (`deadline` supported). A sync function that *returns* a `Promise` still uses the sync path.  
- Extra ticket fields on `unsub` are ignored (only `channel` is read).  
- `Transmitter.Config<TAsync, TMessage, TReturnCases>` — `returnCases` may be a plain type or `Transmitter.Switch<…>` when the return depends on the message key.  
- **Channel map:** browser singleton; SSR → `Context.STORE.transmitterManager` (one `Map` per request). Without a context on the server, APIs throw.

---

## See also

- [Plugins index](../plugins.md)  
- [Contexts](../contexts.md) — SSR store  
- [Initializers — type augmentations](../initializers.md#generated-types-augmentations)