## What Belongs in the Core - The distinction between primitives and composition.

The most dillematic thing I faced while designing this language was what stays in the core and what doesn't. Many things felt like they're obvious to a UI language, but could be reduced  to existing features of the language. The reasoning that helped nd bravely reject them follows such:

The machine, turing complete bunch of transistors is physics. It obeys its own rules whether we touch it or not. To make it do anything we want, we build layers of abstraction on top of it.

Abstraction Layer 1 sits on the hardware. It's repetitive and boilerplate heavy as a consequence of having to describe everything from nothing in the hardware's own terms.

Abstraction Layer 2 is where primitives live. A primitive is an irreducible operation up to the machine.

Abstraction Layer 3 is composition. A composition of primitives.

*paint is a primitive* because it terminates at the GPU. It takes a 32-bit integer, hits the hardware and becomes a pixel color. There's nothing underneath it that the language can expose without becoming the hardware itself.

*rotate is a composition* of primitives. It's sine, cosine, and matrix multiplication arranged in a specific convention. It can be reduced down to primitives, and the primitives so forth.

Appreciate math, it singlehandedly reduced countless primitives that nd bravely embraced. 

The core of this language only provides you with the primitives. The Standard Library, and other community led libraries will contain the composition.

---
<details>

## Primitives – What belongs in the core

### 1. Keywords (16)

These are the irreducible verbs of the language. Each terminates at a different hardware or platform boundary, and no combination of other keywords can replace them.

| Keyword | Why it is a primitive |
|--------|------------------------|
| `import` | Compile‑time name resolution. Irreducible: every module system needs a way to bring external declarations into scope. |
| `global` | Persistent reactive scope that survives navigation. No other keyword provides cross‑page state that automatically updates all references. |
| `go` | Platform navigation instruction. Terminates at the browser or OS history API. |
| `return` | Produces a value from a node. The fundamental exit of expression evaluation. |
| `var` | Reactive mutable cell. Terminates at the dependency‑graph node. All higher‑level state (`data`, `task`, world integration) is built on it. |
| `data` | Declarative asynchronous fetch with automatic refetch and built‑in `.loading` / `.error` / `.value` lifecycle. Irreducible without introducing a lower‑level promise/stream primitive, which would itself be a keyword. |
| `task` | Triggerable asynchronous operation. Same lifecycle as `data`, but fired manually via `.run`. Terminates at the network stack. |
| `if` / `else` | Structural conditional. Irreducible: no math can make a node exist or not exist based on a boolean. |
| `each` | Replication directive. Creates N instances of a subtree from a collection. Irreducible for dynamic lists. |
| `on` | Edge‑detection on signals. The only place where imperative mutation is valid. Separates continuous state from instantaneous side‑effects. |
| `text` | Glyph rasterisation, layout, and rendering. Terminates at the platform’s text shaper and GPU font atlas. |
| `paint` | Fill a region with a colour (or colour‑returning function). Terminates at the GPU fragment shader. |
| `media` | External content (image, video, audio, PDF, 3D model, …) rendered by the platform. Irreducible: the language must never enumerate media formats. |
| `slot` | Content injection point in a component. Irreducible for reusable templates. |
| `world` | Snapshot‑compute‑integrate loop for parent‑defined arrangement of children. Irreducible: no combination of `each`, `var`, and `on` can achieve centralised O(N) computation with child sovereignty. |

### 2. Primitive properties

These are recognised property names that the renderer must implement. They describe the fundamental geometry, appearance, and behaviour of a node. They are not keywords, but they are still primitives because they terminate directly at a rendering or layout operation.

| Property | Why it is a primitive |
|----------|------------------------|
| `pos` | Screen position. Irreducible: the renderer must know where to place a node. |
| `size` | Dimensions of a node. Irreducible for layout. |
| `type` | Typographic scale (size, weight, line‑height, tracking). Terminates at the text shaper. |
| `font` | Typeface selection. Terminates at the platform’s font subsystem. |
| `radius` | Rounded‑corner clipping of the node’s subtree. Terminates at a GPU scissor‑with‑mask operation. |
| `scroll` | Enables scrolling and exposes `.scrollX`/`.scrollY` signals. Terminates at the renderer’s clip‑and‑translate logic. |
| `surface` | Assigns a node to a named rendering plane. Terminates at the GPU render‑pass ordering. |
| `transform` | Accepts a raw 2D affine matrix and pushes it onto the rendering context. Terminates at the GPU vertex shader. |
| `paint` (as property) | Background fill of the node’s implicit rectangle. Same termination as the `paint` content verb. |
| `world` (as property) | Reference to the governing world function. Irreducible. |

### 3. Implicit contexts

These values are always in scope; they are the read‑only output of the renderer’s measurement and layout phases. They cannot be built by user code.

| Context | Why it is a primitive |
|---------|------------------------|
| `self` (`self.x`, `self.w`, `self.b`, …) | The node’s own resolved geometry. Essential for relative positioning. |
| `prev` (`prev.b`, `prev.r`, …) | Previous sibling’s geometry. Irreducible for sequential layouts. |
| `parent` (`parent.w`, `parent.h`, …) | Parent’s geometry. Irreducible for responsive sizing. |
| `viewport` (`viewport.w`, `viewport.scroll`, `viewport.frame`, …) | Root viewport signals. Essential for scroll‑driven animation and responsive breakpoints. |
| `index` | Current iteration index inside `each`. |
| `children` (inside `world`) | Snapshot array of all child instances. |
| `world` (inside `on world.result`) | The result slot for the current child. |
| `p` (`p.x`, `p.y`) | Current pixel coordinate inside SDF expressions. |

### 4. Built‑in signals

Signals originate from hardware or the platform event loop. They are the only sources of change in the reactive graph.

- **Level signals:** `focus`, `pointer.over`, `key.shift`
- **Axis signals:** `viewport.scroll`, `pointer.x`, `scroll.delta`
- **Pulse signals:** `click`, `key.enter`, `blur`, `world.result`

They are primitives because they are the raw interface to the outside world – the language cannot fabricate a mouse event from pure math.

---

## Compositions – What lives in the library

The following features are **not** primitives. They are convenient compositions of existing primitives and belong in the standard library (`layout.ndh`, `sdf.ndh`, `transform.ndh`, …) or in community‑authored libraries.

### Shapes and drawing

| Composition | How it is built from primitives |
|-------------|----------------------------------|
| `circle(cx, cy, r)` | SDF expression `length(p - [cx,cy]) - r` → consumed by `paint` |
| `rect(x, y, w, h)` | `max(abs(p.x - x) - w, abs(p.y - y) - h)` |
| `rounded_rect(x, y, w, h, r)` | Standard SDF combination of `rect` and `circle` |
| `smooth_union(a, b, k)` | `min(a, b) - h*h*k*0.25` after computing blend factor |
| `linear_gradient(c1, c2)` | Function `{ p in mix(c1, c2, p.y / h) }` passed to `paint` |
| `radial_gradient(c1, c2)` | `mix(c1, c2, length(p - center) / radius)` |
| `shadow(blur, offset, color)` | Blurred SDF of the node’s shape, drawn on a lower surface |

### Layouts

| Composition | How it is built from primitives |
|-------------|----------------------------------|
| `stack_y(gap)` | World function that accumulates `y` from children’s heights |
| `stack_x(gap)` | Same, accumulating `x` from widths |
| `grid(cols, gap)` | World function mapping `i` to row/col arithmetic |
| `center` | World function centering children in the parent |
| `masonry(cols, gap)` | World function placing each child in the shortest column |
| `space_between` | World function distributing remaining space as gaps |
| `table_columns` | World function doing two passes: first pass finds max per‑column width, second places |
| `hex_layout(cell_size, gap)` | World function using axial‑to‑pixel hex math |
| `adaptive_layout(gap)` | World function that changes column count based on `pw` |
| `force_graph(repel, attract, dt)` | World function computing N‑body repulsion + edge attraction |
| `gravity(G, dt)` | World function implementing Newtonian gravity simulation |
| `flock(sep, align, coh, dt)` | World function implementing boids algorithm |
| `circular(radius)` | World function placing children in a circle |

### Transforms

| Composition | How it is built from primitives |
|-------------|----------------------------------|
| `rotate(angle)` | Affine matrix `[cos, sin, -sin, cos, 0, 0]` |
| `scale(sx, sy)` | `[sx, 0, 0, sy, 0, 0]` |
| `translate(dx, dy)` | `[1, 0, 0, 1, dx, dy]` |
| `skew(ax, ay)` | `[1, ay, ax, 1, 0, 0]` |
| `compose(m1, m2)` | Matrix multiplication of two 6‑element arrays |

### Media types

The keyword `media` is primitive; the *formats* it supports are not. A video is just `media "clip.mp4"`, an audio file is `media "song.ogg"`, a PDF is `media "doc.pdf"`. The platform decides how to render them. The language never enumerates formats – that is the library’s job.

### Miscellaneous

| Composition | Why it is not a core primitive |
|-------------|---------------------------------|
| `button`, `card`, `modal`, … | Pure component patterns – they are `@node` templates with slots. |
| `router`, `tabs`, `carousel` | Orchestrated with `viewport.path`, `var`, `each`, and `on`. |
| `clip` / `overflow` | Clipping is the default. Overflow scrolling is `scroll y`. No new primitive needed. |
| `opacity`, `blend_mode` | Can be expressed as a `world` override or a `paint` function that reads `backdrop`. |
| `focus_trap`, `tab_order` | Platform accessibility driver concern, not a language feature. |
| `z_index` | Replaced entirely by `surfaces` and declaration order. |

---

The core stops exactly where math begins. The 16 keywords and their associated primitive properties are the *only* things the renderer must understand natively. Everything else – from a simple circle to a galaxy simulation – is just a library function. That is the promise of `.nd`, and the reason its core can stay tiny forever.
</details>