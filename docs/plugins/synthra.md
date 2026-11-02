# Synthra

Optional **Three.js** plugin for a shared WebGL scene on top of the upwwwards frame loop.

| | |
| --- | --- |
| **Enable** | see config below |
| **Access** | `Up.Synthra` |
| **Peer** | `three` |
| **Lazy** | yes — nothing loads until the first `workspace` call |
| **Scope** | client-only (`workspace` throws on the server) |

> **Warning — do not import `three` yourself** (except TypeScript `import type`). Manual runtime imports bypass the Vite lazy graph and can break build-time optimization. Use Three.js only through `Up.Synthra` APIs (`workspace`, and the `three` / `X3D` / `Package` / `Plugin` handles it provides) — access is asynchronous until the peer has loaded.

**Levels:** 🟢 [Essentials](#essentials) (start here) · 🟡 [Going further](#going-further) (optional) · ⬛ [Under the hood](#under-the-hood) (optional)

---

## Essentials

> 🟢 **Essentials** — day-to-day use. Start here.

### Enable

```ts
plugins: [
  {
    name: 'Synthra',
    config: {
      pick: ['Mesh', 'BoxGeometry', 'MeshNormalMaterial'],
      spider: {
        renderer: {
          canvas: '#webgl' // optional existing <canvas>; else Synthra creates one
        },
        camera: {
          type: 'PERSPECTIVE',
          config: { fov: 75, aspect: 1, near: 0.1, far: 10 }
        }
      }
    }
  }
]
```

Install `three`, restart Vite. `WebGLRenderer`, `Scene`, and `PerspectiveCamera` are always included; `pick` adds more exports.

### Minimal scene + page teardown

```ts
import Up from 'upwwwards'

let pack: { clear(): void } | null = null

function build() {
  Up.Synthra.workspace(({ three, X3D, Package, Plugin }) => {
    const cube = new X3D({
      root: new three.Mesh(new three.BoxGeometry(), new three.MeshNormalMaterial())
    })
    cube.setPosition(0, 0, -3)

    pack = new Package(
      cube,
      new (Plugin.get('BASIC'))({
        eTick: () => cube.setRotation(cube.rX, cube.rY + 0.01, cube.rZ)
      })
    )
  })
}

function teardown() {
  pack?.clear()
  pack = null
}

Up.Event.sub([
  ['LIFE', build],
  ['DEATH', teardown]
])
```

Recipe: [recipes — Synthra](../recipes.md#synthra-page-scoped-scene).

---

## Going further

> 🟡 **Going further** — optional. Safe to skip.

Optional.

### Building blocks

| Piece | Role |
| --- | --- |
| **Spider** | Owns renderer, scene, camera |
| **X3D** | Animated wrapper around an `Object3D` |
| **Package** | Groups objects, X3D nodes, and Synthra plugins |
| **Plugin** | Sub-plugin base + tick registry (`Plugin.get('BASIC')`, …) |
| **three** | Loaded constructors (`pick` + core) |

### `Package`

```ts
const pack = new Package(cube, light, basicPlugin)
pack.add(other)
pack.remove(cube)
pack.clear()
```

Removing an X3D aborts its animations; removing a Plugin runs `destroy(bucket, package)`.

### `X3D`

| API | Role |
| --- | --- |
| `setPosition` / `setRotation` / `setScale` | Immediate write |
| `toPosition` / `toRotation` / `toScale` | Animated toward a point |
| `toVisibility(visible, priority?)` | Visibility channel |
| `getPosition` / `getRotation` / `getScale` | Read current point |
| `resetPosition` / `resetRotation` / `resetScale` | Reset channel |
| `abort(flag)` / `abortAll()` | Cancel animation slots |
| `rX` / `rY` / `rZ` | Rotation accessors used in ticks |

```ts
cube.setPosition(0, 0, -5)
cube.toPosition(2, 0, -5)
cube.toScale(2, 2, 2, 0, 1)
cube.toRotation(0, Math.PI, 0, 0, 0, true) // snapped
```

### Built-in plugin: `BASIC`

```ts
const basic = new (Plugin.get('BASIC'))({
  $off: { value: false },
  eTick: ({ Spider }) => { /* every frame while in a Package */ },
  eDestroy: () => { /* when removed from Package */ }
})

basic.config.$off!.value = true // pause ticks
```

### Types

Augment `SynthraBucket` / `SynthraPlugins`: [initializers](../initializers.md#generated-types-augmentations).

---

## Under the hood

> ⬛ **Under the hood** — internals / debug. Safe to skip.

Optional.

- Camera `aspect` is a **multiplier** on `Up.App.lw / Up.App.lh`, not an absolute aspect ratio.  
- Each frame (after load): X3D evaluate → fixed → animate → plugin ticks → render (Thread flag `2048`).  
- **One Spider / shared canvas** per runtime. Multiple independent canvases are not first-class.  
- Attachments (`addAttachment` / eternal) — see JSDoc on `X3D`.  
- `Up.Synthra.add` / `remove` are for **custom bucket keys** (not a substitute for `Package.clear`).  
- `Up.destroy()` runs plugin `[destroy]` for a full runtime teardown.

---

## See also

- [Plugins index](../plugins.md)  
- [Core — Thread / Event](../core.md)  
