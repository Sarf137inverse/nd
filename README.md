# .nd Language Specification
## Complete Reference v0.3

---

## 1. Origin and Philosophy

The web is three languages pretending to be one system. HTML describes
structure. CSS describes appearance. JavaScript describes behavior. They
were never designed to share a model. They communicate through hacks.

This language starts from zero. One question: what is a screen, and what
does a language that describes it actually need?

The answer: one primitive, one reading direction, one model. Everything
else is a consequence.

**Core principles:**

Every keyword earns its place or gets cut. One concept per keyword.
One job per keyword. If a keyword can be replaced by a simpler mechanism
that already exists, it is cut. No exceptions made for familiarity,
developer friendliness, or historical precedent.

Developer friendliness is a side effect of an elegant model. When the
model is simple, the usage is simple. We do not design around convenience.
We design around truth.

---

## 2. Syntax Rules

```
1. Indentation defines hierarchy. Children indent under parents.
2. One concept per line.
3. Reading direction: top to bottom, always.
4. Space separates values on the same line.
5. Punctuation only where it reduces noise or resolves genuine ambiguity.
6. No semicolons. Ever.
```

Punctuation rules:
```
( )    function calls and math grouping
[ ]    array literals and index access
{ }    map literals and string interpolation
,      separates values of different kinds in the same position
       (each posts post, index — item vs index, different kinds)
       (function args where ambiguity exists without it)
?  :   ternary operator
0x     hex integer prefix
```

---

## 3. The One Primitive — Node

Everything is a node. A page is a node. A button is a node. A physics
body is a node. A behavior fragment is a node. A pure math expression
is a node.

**Formal definition:**

A node is a named or anonymous declaration in a document tree that
produces a value, state, visual output, or behavior which other nodes
can reference.

Nothing else defines a node. Position, size, content, behavior — all
optional. A node with none of these is still a node. It exists. It has
a name. Other nodes can reference it.

**Declaration:**

```
@name
  property value
  property value

  @child
    property value
```

`@name` declares a named node. The `@` sigil is the identity marker.
The same name anywhere in scope refers to the same node.

Anonymous nodes have no `@name`. They exist, render, but cannot be
referenced externally.

**`@` is a pointer.**

`@name` declares and addresses. Declaration creates the node. Reference
reads it. Same sigil, same name, different grammatical positions.

```
@btn           declaration — creates and names the node
@btn.w         reference   — reads btn's resolved width
@btn.scroll    reference   — reads btn's scroll position
@btn.liked     reference   — reads btn's var named liked
```

---

## 4. Units

Two numeric systems. Distinguished by literal form:

```
Integer    no decimal point    absolute units resolved by renderer
           200  52  0  -32  9999

Float      decimal point       decimal arithmetic values
           0.08  1.6  -0.1  3.14159

Norm       decimal point       semantically bounded 0.0 to 1.0
           0.5  0.3  1.0  0.0  (type inferred from context)
```

**Norm vs Float:**

Syntactically identical. Type determined by context. `pos 0.5 0.5` — pos
expects norm, so both values are norm. `k * 0.5` — arithmetic context,
0.5 is float.

Norm violations are build errors where statically detectable:
```
pos 1.5 0.5    // build error — 1.5 exceeds norm range
pos -0.1 0.5   // build error — negative norm
```

Norm arithmetic:
```
norm * norm    → norm     (product of two norms is always norm)
norm * float   → float    (may exceed bounds — compiler warns)
1.0 - norm     → norm     (compiler proves range statically)
```

**Dimensional analysis:**

```
float × int    → absolute
int + int      → absolute
float + float  → float
int + float    → build error (ambiguous — caught at compile time)
```

**Color:**

Colors are 32-bit integers written with `0x` prefix:
```
0xrrggbbaa    eight hex chars — explicit alpha
0xrrggbb      six hex chars  — implicit ff alpha (fully opaque)
```

No shorthand forms. No `#` prefix. `0x` only.

Color is an int with semantic convention. The `0x` prefix signals intent.
Math on colors is math on integers:
```
0x0055ff00 + (k * 255)    // adds alpha channel via arithmetic
```

---

## 5. Type System

Types are guarantees about valid operations. Always inferred. Never
declared. Checked at build time. The runtime performs no type checking —
it is already done.

**Complete type list:**

```
int       whole number         200  -32  0
float     decimal number       0.08  -0.1  3.14
norm      bounded 0.0-1.0      0.5  1.0  0.0
bool      true or false        true  false
string    UTF-8 text           "hello"  ""
array     ordered collection   [1 2 3]  ["a" "b"]
map       keyed collection     {low 0x00ff00ff, _ 0xffffffff}
node      a tree node          @btn  @card
signal    changing value       viewport.scroll  click  k
null      absent value         null
```

**Type inference sources:**

```
Literal form    0x0055ffff → int(color)
                200        → int
                0.5        → float or norm (context)
                true       → bool
                "hello"    → string
                []         → array
                {}         → map

Usage context   pos expects norm
                paint expects int
                arithmetic expects numeric

Propagation     int + int → int
                string + any → string (concatenation)
                float * int → float
```

**One automatic coercion:**

```
int → float    automatic. 200 becomes 200.0 where float expected.
```

Everything else is explicit. No silent surprises.

**Null:**

Represents absent value. Valid only in contexts that accept null.
`??` handles null explicitly:
```
post.subtitle ?? "No subtitle"
```

Null in arithmetic is a build error. Null in string context is a
build error.

---

## 6. Literals

**Integer:**
```
200    52    0    -32    9999
```
Negative literals: `-32` is one token. Not `-(32)`.

**Float:**
```
0.08    1.6    0.02    -0.1    3.14159
```
Always has a decimal point.

**Norm:**
Syntactically identical to float. Type from context. See section 4.

**Bool:**
```
true    false
```
Reserved. Cannot be used as identifiers.

**String:**
```
"hello"
"Design is constraint."
"/api/posts"
""
```
Double-quoted. UTF-8. Escape sequences:
```
\"    literal quote
\\    literal backslash
\n    newline
\t    tab
```

Multi-line:
```
"
  First paragraph.

  Second paragraph.
"
```
Leading and trailing newlines stripped. Indentation preserved relative
to least-indented line.

**String interpolation:**
```
"Hello {user.name}!"
"Page {page} of {total}"
"/api/post/{params.id}"
"Track {index + 1} of {library.value.length}"
```
`{expression}` inside any string. Any valid expression. Auto-converts
to string. No nested string literals inside `{}` — use a var instead.

Type conversions to string:
```
int    → "42"
float  → "3.14"
norm   → "0.5"
bool   → "true" / "false"
null   → ""
array  → build error (ambiguous)
```

**Color:**
```
0x0055ffff    0xff3366ff    0x0a0a0fff
```
See section 4.

**Array:**
```
["work" "design" "code"]
[0 1 2 3]
[0x0055ffff 0xff3366ff]
[]
```
Square brackets. Space-separated. Homogeneous type — all elements
same type. Mixed type is a build error. Empty array type inferred
from first use.

**Map:**
```
{
  low    0x2ecc71ff,
  medium 0xf39c12ff,
  high   0xe74c3cff,
  _      0xccccccff
}
```
Curly braces. Key-value pairs. Comma between entries. Keys are bare
identifiers. Values are any type — homogeneous. `_` is catch-all key.

Inline form:
```
{ok 0x00ff00ff, error 0xff0000ff}
```

**Null:**
```
null
```

---

## 7. Properties

**Formal definition:**

A property is a typed constraint on a node. It faces inward. It
constrains the declaring node's geometry, appearance, or behavior.
It has no output. It does not cascade to children. It is evaluated
once per frame in pre-computed dependency order.

**Three kinds:**

```
Geometric     pos size radius scroll surface
              constrain where the node is and how much space it occupies

Visual        paint font scale
              constrain how the node appears

Behavioral    var slot world
              constrain what the node does or accepts
```

**Property evaluation:**

Evaluated in dependency order. The compiler traces the dependency graph
at build time, produces a flat ordered evaluation list. The renderer
executes the list once per frame. No tree traversal per frame.

Circular dependencies are build errors:
```
@a pos @b.r 0.0    // a depends on b
@b pos @a.r 0.0    // b depends on a — circular — build error
```

**Property inheritance:**

Properties never cascade. A property on a parent has zero effect on
children unless explicitly referenced via `parent.*`.

**if at property level:**

Properties can be conditionally overridden:
```
@btn
  paint 0x0055ffff

  if pointer.over
    paint 0x0044ccff
    scale 1.02

  if pointer.down
    paint 0x0033bbff
    scale 0.97
```

`if` at property level conditions which property values apply.
Evaluated continuously. Accepts property declarations only — no child
nodes, no mutations, no content verbs. Multiple `if` blocks compose
in declaration order — last true condition wins per property.

---

## 8. Geometric Properties

### pos
Position within parent. Two norm values. Cartesian 0.0–1.0.
```
pos 0.5 0.5    // center
pos 0.0 0.0    // top-left
pos 1.0 1.0    // bottom-right
pos 0.75 0.3   // precise placement
```

### size
Physical dimensions. Two values: width and height.
```
size 200 52      // absolute width × height
size 1.0 0.5     // norm fraction of parent
size _ _         // fit to content (both axes)
size 1.0 _       // full parent width, fit height
```

`_` (fit): a built-in value expression. Evaluates to content's
measured size. Not a type — a value the renderer computes.

### radius
Corner geometry.
```
radius 8       // absolute, all corners
radius 0.5     // norm — fraction of smaller dimension (pill shape)
```

### scroll
Makes container clip and scroll its content on an axis.
```
scroll y       // vertical scroll
scroll x       // horizontal scroll
scroll both    // both axes
```

Content is clipped to container bounds. Content that would overflow
is navigable via scroll signals. `@name.scroll` exposes current
scroll position as a readable signal.

For exotic scroll behavior — momentum, custom curves, linked containers
— use the manual pattern:
```
import hid/scroll

var offset 0

on scroll.delta
  max = @content.b - self.h
  offset = clamp(offset + scroll.delta, 0, max)

@content
  pos 0.0 (0 - offset)
  size 1.0 _
```

### surface
Which named surface this node paints onto. Declared in surfaces.ndh.
```
surface float    // paints above base layer
surface modal    // paints above float layer
```

---

## 9. Visual Properties

### paint
Applies color to the node's region. Takes any 32-bit integer.
```
paint 0x0055ffff    // fully opaque blue
paint 0x0055ff80    // 50% opacity blue
paint 0x0055ff00 + (k * 255)    // alpha driven by signal
```

`paint` is a content verb and a property. As a property: fills the
node's background. As a content verb: draws after SDF computation.
Context determines which — inside SDF drawing context vs standalone.

### font
Typeface name. Rendering is the device's problem.
```
font Inter
font system-mono
font system-serif
font system
```

Declared in header files:
```
// fonts.ndh
font Inter  "/fonts/inter.woff2"
```

### scale
Uniform transform applied to the node.
```
scale 0.97    // slightly shrunk
scale 1.05    // slightly grown
```

---

## 10. Behavioral Properties

### var
Declares reactive mutable state. Type inferred from initial value.
Locked after declaration. Changes via `on` blocks. Everything that
references a `var` re-evaluates automatically when it changes.

```
var count 0
var open false
var name ""
var tags ["work" "design"]
var priority_color {
  low    0x2ecc71ff,
  medium 0xf39c12ff,
  high   0xe74c3cff,
  _      0xccccccff
}
```

`var` inside `each`: each iteration produces a distinct node instance.
Each instance owns its own `var` state. No shared state across
iterations. The `@` pointer is the identity. State follows identity.

Type mismatch in mutation is a build error:
```
var count 0
on click
  count = "hello"    // build error — count is int not string
```

### slot
Marks a content injection point in a component. The component declares
where. The call site declares what.

```
// component definition
card(w h color)
  size w h
  paint color
  slot
```

Named slots for multiple injection points:
```
card(w h color)
  size w h
  paint color
  slot header
  slot body
```

Usage:
```
@post_card card(1.0 _ 0x1a1a1aff)
  header
    text post.title
      type 18 700
  body
    text post.body
      type 14 400
```

### world
Declares a governing function for this node's children.
One per node maximum. See section 16 for complete world model.

```
@solar_system
  world @gravity(6.674e-11 viewport.frame)
```

---

## 11. Content Verbs

**Formal definition:**

A content verb is an instruction to the renderer. Where a property
faces inward — constraining the node — a content verb faces outward.
It tells the renderer to produce something. Content verbs do not
constrain each other. Multiple content verbs in one node are
independent renderer instructions executed in declaration order.

### text
Flowing typographic content. Always flows — wraps, breaks into lines.
A one-line label is a flow whose content happens to be one line.
Content is pure Markdown.

```
@title
  pos 0.5 0.4
  text "Design is constraint."
    type 72 800
    paint 0xffffffff

@article
  pos 0.1 0.1
  text "
    First paragraph. *Markdown* works here.

    Second paragraph. Blank line separates paragraphs.
  "
    type 16 400 1.6
    font Inter
```

Inline Markdown constructs:
```
*emphasis*
**strong**
`verbatim`
[text](/url)
~~struck~~
^super^
~sub~
```

### type
Typography properties for text content. Positional:
font-size → weight → line-height → letter-spacing

```
type 16              // font-size only
type 16 400          // font-size + weight
type 16 400 1.6      // + line-height
type 16 400 1.6 0.02 // + letter-spacing
```

`type` only appears inside text content blocks. Build error anywhere
else.

### paint (as content verb)
Applies color. In SDF context — draws accumulated SDF computation.
Outside SDF context — fills node background.

### media
External content. Source is a file path or URL. Platform decodes.
Language never knows or cares about format.

```
@hero
  media "/photo.jpg"
    fit cover

@clip
  media "/film.mp4"
    autoplay true
    muted true

@track
  media "/sound.mp3"
    loop true

@model
  media "/scene.gltf"
```

`fit` values: `cover`, `contain`, `fill`, `none`.
Type-specific properties (`autoplay`, `muted`, `loop`) are validated
by the media type's header file.

---

## 12. SDF Drawing

Shapes are mathematical functions — Signed Distance Functions. They
are not keywords. They are functions from standard libraries imported
by the author. The core language provides one thing: the implicit
pixel coordinate `p`.

**`p` — implicit pixel coordinate:**

Inside any SDF expression, `p` is the current pixel position.
Two components:
```
p.x    horizontal position within node bounds (norm, 0.0-1.0)
p.y    vertical position within node bounds (norm, 0.0-1.0)
```

**SDF expressions in nodes:**

Any expression that returns a float (signed distance) shapes the node:
```
@circle_node
  smooth_union(circle(0.3, 0.6, 0.4), circle(0.7, 0.4, 0.3), 0.08)
  paint 0x4488ff1c
```

The function call returns a signed distance field. The renderer uses
it to determine inside/outside for each pixel. Negative = inside.
Positive = outside. Zero = boundary.

No `sdf` keyword. The function's return type carries the meaning.

**Multiple draw calls — sequential:**
```
@blob
  smooth_union(circle(0.3, 0.6, 0.4), circle(0.7, 0.4, 0.3), 0.08)
  paint 0x4488ff14

  circle(0.9, 0.7, 0.3)
  paint 0x2483ff08
```

Each SDF expression followed by `paint` is one draw call. Draw calls
layer in declaration order.

**Standard library — sdf.ndh:**

SDF shapes are not in the core language. They are library functions:
```
// sdf.ndh
@circle cx cy r
  return length(p.x - cx, p.y - cy) - r

@rect cx cy w h
  return max(abs(p.x - cx) - w, abs(p.y - cy) - h)

@rounded_rect cx cy w h r
  var qx = abs(p.x - cx) - w
  var qy = abs(p.y - cy) - h
  return length(max(qx, 0.0), max(qy, 0.0)) - r

@smooth_union a b k
  var h = max(k - abs(a - b), 0.0) / k
  return min(a, b) - h * h * k * 0.25
```

Usage:
```
import sdf.ndh

@blob
  smooth_union(circle(0.3, 0.6, 0.4), circle(0.7, 0.4, 0.3), 0.08)
  paint 0x4488ff1c
```

**Math primitives (expression evaluator built-ins):**
```
sqrt(x)           square root
abs(x)            absolute value
min(a, b)         minimum
max(a, b)         maximum
clamp(x, lo, hi)  clamp value to range
length(dx, dy)    2D vector magnitude — sqrt(dx²+dy²)
sin(x) cos(x)     trigonometric
floor(x) ceil(x)  rounding
```

---

## 13. Geometric Context

Always in scope. Never declared. Provided by the renderer after layout
resolves. Dot-access. Read-only.

```
self.x    self.y    self.w    self.h    self.b    self.r
prev.x    prev.y    prev.w    prev.h    prev.b    prev.r
parent.x  parent.y  parent.w  parent.h  parent.b  parent.r
```

```
.x    left edge position
.y    top edge position
.w    width
.h    height
.b    bottom edge  (= .y + .h)
.r    right edge   (= .x + .w)
```

**`prev` — previous sibling:**

`prev` refers to the previous sibling in the same parent. Inside `each`,
it refers to the previous iteration's instance. `prev` on the first
iteration defaults to all-zero.

First-item spacing — use an explicit placeholder:
```
@_gap
  size 0.0 -24

each posts post
  @card
    pos 0.0 prev.b + 24
```

**`parent` — parent node:**

```
size parent.w - 280 1.0    // width = parent width minus 280 units
pos @sidebar.r 0.0          // x = right edge of sidebar
```

**Viewport — implicit root node:**

```
viewport.w        viewport width
viewport.h        viewport height
viewport.scroll   current scroll position
viewport.path     current URL path
viewport.frame    delta time in ms since last frame (signal, fires every frame)
```

---

## 14. Signals

**Formal definition:**

A signal is a value that changes over time. Not an event. Not a
callback. A value. Used anywhere a value is expected. No special
syntax for consuming signals — they are just values in expressions.

**Three kinds:**

```
state     continuously true or false        bool
          pointer.over  focus  key.shift

axis      continuously varies               float or norm
          viewport.scroll  pointer.x  k

pulse     true for exactly one frame        bool
          click  key.enter  blur
```

These three are exhaustive. Every time-varying value is one of these
three. No fourth kind exists.

**Implicit runtime signals (always in scope):**

```
click      pulse    fires when node is activated (mouse, touch, keyboard)
focus      state    true when node owns input routing
blur       pulse    fires when focus leaves node
```

**Signal composition:**

Signals compose through math. No special composition API:
```
viewport.scroll 0 300 as k    // bind raw value to name k
k * k                          // transform — quadratic
0x0055ff00 + (k * 255)        // drives paint
```

`as` names any value. `k` is not a special normalized variable —
it is the raw value within the declared range. Normalization is
the developer's math:
```
viewport.scroll 0 300 as k
  var t = k / 300              // normalize to 0.0-1.0
  var curve = t * t            // quadratic
  paint 0x0055ff00 + (curve * 255)
```

---

## 15. Control Flow

### if / else

**Formal definition:**

`if` is a condition on existence at node level. The node inside exists
only when the condition is true. Not hidden — absent. No memory, no
geometry, no render cost.

At property level: conditions which property values apply.

```
// existence-level
if posts.loading
  @spinner
    pos 0.5 0.5

else if posts.error
  @error_msg
    pos 0.5 0.5

else
  @feed
    pos 0.0 0.0
```

Evaluated every frame. First true condition wins. Exactly one branch
exists at any time.

`if` at property level — see section 7.

### on

**Formal definition:**

`on` fires once when its operand transitions to a truthy state.
It is the only place in the language where imperative mutation is valid.

Truthy transitions:
```
bool expression    false→true
value expression   null→non-null
pulse signal       fires on that frame
```

`on` never fires while the operand stays truthy. Only on transition.

```
on click
  count = count + 1

on focus
  open = true

on pointer.over        // fires once when pointer enters — not every frame
  entry_count = entry_count + 1

on @submit.value       // fires when task completes (null→value)
  go "/" + @submit.value.id

on count > 10          // fires when count transitions from ≤10 to >10
  show_warning = true
```

Valid inside `on` blocks:
```
Assignment        count = count + 1
Node task calls   @submit.run
Navigation        go "/url"
Conditionals      if count > 10
```

Invalid inside `on` blocks:
```
Content verbs     text "hello"      // build error
Properties        paint 0x0055ff    // build error
each loops        each posts post   // build error
```

### each

**Formal definition:**

`each` is a replication directive. It declares that a node tree exists
once per item in a collection. Not a loop — a declaration of N instances.

```
each posts post              // collection, item alias
each posts post i            // collection, item alias, index alias
each 0 10 i                  // range (exclusive end), alias
each 0 10 2 i                // range with step, alias
posts | filter(x) | each post // pipe into each
```

Type of first token determines form:
```
identifier first    → collection form
number first        → range form
```

`prev` scopes to innermost `each`. First iteration `prev` is all-zero.
`index` available when bound.

**Nested each:**
```
each categories category
  each category.items item
    @card
      pos 0.0 prev.b + 16    // prev = previous card, innermost scope
```

### Ternary
Single-line binary value selection. Used in value positions only:
```
paint liked ? 0xff3366ff : 0x1a1a1aff
text count > 99 ? "99+" : count
size playing ? 56 : 44
```

### ?? (Null coalescing)
Runtime fallback when a value is absent:
```
text post.subtitle ?? "No subtitle"
text posts.error.message ?? "Something went wrong."
```

---

## 16. Logic and State

### var
See section 10. Declared with initial value. Changed via `on`.
Reactive — all dependents update automatically.

### data
**Formal definition:**

`data` is a `var` whose value comes from outside the program. It
manages its own async lifecycle. Everything that references it
updates automatically when it arrives.

```
data posts from "/api/feed"
data user from session
```

Three properties always available:
```
posts.loading    bool   — true while fetching
posts.error      bool   — true if fetch failed
posts.value      any    — the data, once arrived
posts.error.message    string — error description
```

Refetches automatically when its source expression changes:
```
data post from "/api/post/" + params.id
// params.id changing triggers refetch automatically
```

Manual refetch via var:
```
var version 0
data posts from "/api/feed?v=" + version

on click
  version = version + 1    // triggers refetch
```

### task
**Formal definition:**

`task` is a `data` that you trigger. Same lifecycle — loading, error,
value — but fires on demand rather than automatically.

```
@submit task → "/api/post"
```

Firing:
```
on click
  @submit.run
    send content
```

Reading state:
```
if @submit.loading
  text "Posting..."

on @submit.value
  go "/" + @submit.value.id
```

`@submit` is a node. Named. Lives in scope. No geometry. Holds async
state readable via `@` pointer from anywhere in scope.

**Multiple runs:**
```
on click
  if not @submit.loading    // gate second click
    @submit.run
      send content
```

### Parameterized nodes (replacing fn)
Named reusable expressions. Parameters are named holes. Call site
fills them. Pure nodes are inlined by compiler — zero runtime cost.

```
@double n
  return n * 2

@clamp n lo hi
  if n < lo
    return lo
  if n > hi
    return hi
  return n

@excerpt text
  return text.slice(0, 120)
```

Usage:
```
size @double(self.w) fit
text @excerpt(post.body)
paint @clamp(scroll_val, 0, 255) * 0x00000001
```

Parameters with defaults:
```
@fade color=0x0055ffff alpha=1.0
  return color + (alpha * 255)
```

**return:**

Names the value a node produces. Exits evaluation immediately.
First return reached wins.

**Pure vs stateful nodes:**

```
Pure        only return + math. no var, no on, no signals.
            compiler inlines at call site. zero runtime cost.

Stateful    contains var, on, signals, or children.
            lives in tree. allocated once at scene load.
```

### Operators

```
Arithmetic    + - * / %
Comparison    == != < > <= >=
Boolean       and  or  not       (words — no shift-key noise)
Ternary       ? :
Null coal.    ??
Pipe          |
```

**Operator precedence (standard mathematical convention):**
```
1. ( )           grouping
2. @name(args)   function calls
3. .             property access
4. - (unary)     negation
5. * / %         multiplicative
6. + -           additive
7. < > <= >=     comparison
8. == !=         equality
9. not           boolean not
10. and          boolean and
11. or           boolean or
12. ??           null coalescing
13. ? :          ternary
14. |            pipe
15. as           naming
```

**`+` overloading:**

`+` is arithmetic when both operands are numeric. Concatenation when
either operand is a string. Ambiguous type = build error.

**`as` — immutable alias:**

Names any value. Scoped downward from declaring node. Type locked at
declaration. Names the value, never the operation.

```
paint 0xff45ff34 as brand
radius 12 as soft
size 200 120 as card_size
viewport.scroll 0 300 as k
```

---

## 17. Pipes

`|` in value positions only. Pure data transformation. Left to right.
No side effects. Each step is a function — input in, output out.

```
post.body | excerpt | text
  type 15 400

posts | filter(published) | each post
  @card
    pos 0.0 prev.b + 24

viewport.scroll | map(0, 300, 0x0055ffff, 0x0055ff00) | paint
```

Multi-line for longer chains:
```
var result posts
  | filter(published)
  | sort(date)
  | take(10)
```

**Valid pipe terminals:**
```
property name    receives value — sets that property
content verb     text image paint media — renders value
each             receives array — iterates it
```

Type mismatch at any seam is a build error. Compiler traces types
through entire chain before producing .ndb.

Side effects (`on`, `go`, `task`) are never in pipes. Pipes are
pure value transformation. Side effects belong in `on` blocks.

---

## 18. Input — Hardware Signals

Input devices are not baked into the language. Every hardware signal
lives in a header file. Imported explicitly. The language has zero
opinion on what hardware exists.

**Signal declaration syntax (in .ndh files):**

```
signal name    kind    raw.platform.address
```

Right side is always an address. Never an expression. The platform
driver does all computation before this point. The header only names.

**Three signal kinds — exhaustive:**

```
state    bool — continuously true or false right now
axis     float — continuously varies
pulse    bool — true for exactly one frame
```

**Standard headers:**

```
// hid/pointer.ndh
signal pointer.over    state    raw.pointer.bounds_hit
signal pointer.down    state    raw.pointer.button0
signal pointer.x       axis     raw.pointer.x
signal pointer.y       axis     raw.pointer.y

// hid/keyboard.ndh
signal key.enter       pulse    raw.key.enter_pulse
signal key.shift       state    raw.key.shift_held
signal key.space       pulse    raw.key.space_pulse
signal key.char        pulse    raw.key.char

// hid/scroll.ndh
signal scroll.delta    axis     raw.scroll.delta
signal scroll.x        axis     raw.scroll.x
signal scroll.y        axis     raw.scroll.y

// hid/touch.ndh
signal touch[]         state    raw.touch.points
```

Multi-instance devices use array signals:
```
each touch t
  @ripple
    pos t.x t.y
    circle(0.5, 0.5, 0.04)
    paint 0x0055ff80
```

Namespace collisions:
```
import hid/pointer as ptr
import hid/custom-rig as rig

ptr.over
rig.flex_sensor_3
```

**Forward compatibility:**

New hardware requires zero language changes. Manufacturer ships a
.ndh header. Done. The language never knows what hardware exists.

---

## 19. Components and Reuse

**Definition:**

A named property set in a .ndh file. Parameters are space-separated,
positional, type-inferred.

```
// components.ndh
import tokens.ndh

card(w h color)
  size w h
  radius soft
  paint color
  slot
```

**Application:**

```
@post_card card(1.0 _ 0x1a1a1aff)
  pos 0.0 prev.b + 24
  // children inject into slot
  text post.title
    type 18 700
```

**Parameter types:**

Inferred from usage inside template body. Mismatch at call site is
build error. Unused parameter is build error.

**Overrides:**

Node block properties take precedence over template properties:
```
@featured card(1.0 _ 0x1a1a1aff)
  radius 24         // overrides template's radius
  paint 0x0055ffff  // overrides template's color parameter
```

**Slots:**

Single slot:
```
card(w h color)
  size w h
  paint color
  slot
```

Named slots:
```
article_card(w h)
  size w h
  paint surface
  slot header
  slot body
```

Slot vs override disambiguation: name with value on same line =
property override. Name alone with block below = slot fill.
```
@piece article_card(1.0 _)
  radius 24          // override — value on same line
  header             // slot fill — block below
    image post.thumbnail
```

---

## 20. Navigation

```
go "/about"
go "/post/" + post.id
go "/product/" + item.id
```

`go` is a content verb. Instructs the browser to navigate to a URL.
The browser handles history, URL bar, back gesture. The language
handles nothing else about navigation.

Back navigation: the browser's native gesture. No language keyword.

SPA-style navigation via `viewport.path`:
```
data content from "/api/view" + viewport.path

@shell
  @nav
    // renders once, never re-renders on navigation

  @content
    if content.loading
      @spinner
    else
      // render content.value
```

The developer chooses: full page loads (separate .nd files + `go`)
or SPA behavior (`viewport.path` + `data`). The language supports
both through composition.

---

## 21. Layering — Surfaces

Surfaces control paint order. Declared in a header file. Ordered by
declaration — first declared is bottom, last declared is top.

```
// surfaces.ndh
surfaces {
  base
  float
  modal
}
```

Usage:
```
@nav
  surface float    // paints above all base-surface nodes

@dialog
  surface modal    // paints above float-surface nodes
```

Every node paints to `base` by default. No change to existing
behavior without explicit `surface` declaration.

---

## 22. Scope

**Formal definition:**

Scope is the set of names visible at a point in the tree. All name
resolution happens at build time. The runtime never looks up a name —
only addresses.

**The rule:**

A name is visible to everything below and beside it in the same branch.
Never upward. Never across branches.

```
@page
  paint 0x0055ffff as brand    // declared here

  @nav
    paint brand                 // valid — below declaration

  @hero
    paint brand                 // valid — beside @nav, below declaration

    @title
      paint brand               // valid — below @hero

@other_page
  paint brand                   // build error — different branch
```

**Three scope levels:**

```
File scope      top of .nd file — visible everywhere in this file
Node scope      inside a node — visible to children and siblings below
Block scope     inside if/each/on — visible only within that block
```

**`@` pointer scope:**

Siblings are visible. One level up is visible. Deeper levels require
explicit export:

```
@page
  @sidebar
    @logo
      size 120 40
    size @logo.w as logo_width    // export value, not the node

  @main
    pos @sidebar.r 0.0            // @sidebar is sibling — valid
    // @logo not accessible — inside @sidebar
    // logo_width is accessible — exported by @sidebar
```

**`global` scope:**

Sits above file scope. Visible everywhere in every file in the session.
Local scope always wins over global scope.

**`import` scope:**

Declarations enter file scope at the point of import. Not hoisted.
Available from import line downward.

Collision between imports: build error. Resolve with explicit qualification:
```
import theme_a.ndh as a
import theme_b.ndh as b

paint a.brand
paint b.brand
```

---

## 23. Global State

```
global
  var cart []
  var theme "dark"
  data user from session
  @format_date d
    return d.slice(0, 10)
```

`global` accepts: `var`, `data`, parameterized nodes.
`global` rejects: `scene`, `task`, components, anything else.

Global scope persists across navigation. All files in the session
can read and write global state. Global `var` changed in one page
is reflected immediately in all other pages that reference it.

---

## 24. The World Model

**The breakthrough:**

Every existing layout system asks each child to compute its own
position relative to its neighbors. This requires every child to
know about the system it's part of — information that belongs to the
parent, not the child. For complex systems (masonry, N-body, center-
of-mass), it requires O(n²) redundant recomputation.

The world model separates the computation from the nodes entirely.
The parent declares a governing function. The function receives the
whole child set at once. It computes everything centrally. O(N).
Children receive results and integrate however they want.

**Three phases, one per frame:**

```
Snapshot       runtime reads children's current state. read-only.
               children are unaffected. they don't know it's happening.

Computation    one function. receives ALL children. one pass. O(N).
               pure — no side effects. returns one result per child.

Integration    each child receives its result via world.result pulse.
               uses what it wants. ignores the rest. total freedom.
```

**`world` property:**

Declared on parent. Names the governing function.

```
@solar_system
  world @gravity(6.674e-11 viewport.frame)
```

One per node maximum. Build error if function doesn't have correct
shape (accepts `children` implicitly, returns array).

**`children` — implicit context inside world functions:**

Read-only array. One element per child. Same order as declaration.
Each element exposes all the child's declared `var`, properties,
and geometric context via dot-access.

```
children[0].pos       // child's var named pos
children[0].mass      // child's var named mass
children[0].w         // child's resolved width
children.length       // number of children
```

Available automatically inside world functions. Never declared.
Read-only — mutations during computation are build errors.

`children` inside `each`: all N instances appear. Not the template —
the actual instances.

Nested worlds: inner world sees only its own children. Outer world
sees the inner parent as one child. Never inside it.

**World function signature:**

```
@gravity G dt
  // children implicit — always available, read-only
  // G and dt are explicit parameters from call site
  var results []

  each children body i
    // computation
    results = results + [{ pos new_pos  vel new_vel }]

  return results    // array, length == children.length
```

Pure function. No `on`, `go`, `task`, external mutation. Build error
if side effects detected. Same inputs always produce same outputs.

Return type: array of maps. One map per child. Same length as
`children`. Keys are property names or var names. Values are computed
results. Child decides which keys to read. Empty map `{}` valid for
children needing no result.

Length mismatch is a runtime error (caught first frame):
```
world function @gravity returned 3 results for 4 children
```

Nested maps valid:
```
{ physics {pos vel}  visual {paint scale} }

// child reads:
world.physics.pos
world.visual.paint
```

**`world.result` — integration pulse:**

Fires on each child simultaneously after world computation completes,
before layout resolves. Once per frame. A pulse signal.

```
@earth @planet([0.5 0.5] [0.0 0.001] 5.972e24)
```

```
@planet(pos vel mass)
  var pos pos
  var vel vel
  var mass mass

  on world.result
    pos = world.pos
    vel = world.vel
```

`world.*` inside `on world.result`: reads this child's result slot.
The runtime maps child to its index — child never knows its own index.

```
world.pos        // this child's pos from result
world.vel        // this child's vel from result
world.paint      // this child's paint from result
```

Children that declare no integration keep their last state. They still
participate in the snapshot — other bodies are affected by them — but
they don't move. A fixed star.

**Reuse via parameterized nodes:**

Common integration patterns packaged as parameterized nodes:
```
@planet(pos vel mass)
  var pos pos
  var vel vel
  var mass mass

  on world.result
    pos = world.pos
    vel = world.vel

@earth @planet([0.5 0.5] [0.0 0.001] 5.972e24)
@mars  @planet([0.3 0.3] [0.001 0.0] 6.39e23)
@venus @planet([0.7 0.4] [0.0 -0.001] 4.867e24)
```

**Frame sequence:**

```
1. Input signals sampled       hardware signals read once, frozen
2. Reactive updates            var, if, data, task states update
3. Snapshot                    children read for each world node
4. World computation           pure functions run — parallelizable
5. world.result pulse          fires on all children simultaneously
6. Integration                 on world.result executes, var updates
7. Layout resolves             geometric context computed
8. Render                      renderer draws current state
```

Inner worlds run before outer worlds (depth-first).
Multiple independent worlds on different parents run in parallel.
Frame budget enforced — exceeded budget produces warning, children
keep last state.

**Failure cases:**

```
Length mismatch     runtime error, first frame. children keep last state.
Type mismatch       build error (static) or runtime error (dynamic).
Side effects        build error. compiler scans world function bodies.
Budget exceeded     runtime warning. result skipped. children hold state.
Child removed       result discarded. next frame snapshot shorter.
```

**What this enables:**

Any layout, any physics, any relational behavior, packaged as a
library function applied to a parent node:

```
@feed
  world @stack_y(16)
  // children lay out automatically

@galaxy
  world @gravity(G viewport.frame)
  // children simulate gravity

@grid_layout
  world @masonry(3, 12)
  // children tile into masonry grid

@interactive
  world @force_directed(repulsion attraction)
  // children find equilibrium
```

O(N). One pass. Every frame. Packaged as libraries. Author writes
nothing about positioning inside children.

---

## 25. Import System

```
import tokens.ndh
import components.ndh
import layout.ndh
import sdf.ndh
import hid/pointer
import hid/keyboard
import media/video
```

Compile-time directive. Zero runtime cost. The compiler resolves all
imports before producing .ndb. The binary contains no import statements.

Aliased imports to avoid collision:
```
import theme_a.ndh as a
import hid/pointer as ptr
```

All declarations in a header file enter scope unprefixed. Collision
between two imports = build error. Resolve with aliases.

**Standard library (community-maintained .ndh files):**

```
tokens.ndh       design tokens — colors, radii, sizes
components.ndh   reusable node templates
layout.ndh       stack_y, stack_x, grid, masonry — world functions
sdf.ndh          circle, rect, rounded_rect, smooth_union
math.ndh         clamp, lerp, map, normalize — math utilities
hid/pointer      pointer signals
hid/keyboard     keyboard signals
hid/scroll       scroll signals
hid/touch        touch signals
media/video      video-specific properties
media/audio      audio-specific properties
surfaces.ndh     surface layer declarations
```

**Header file format (.ndh):**

Same syntax as .nd. Contains declarations only — no scene-level content.
Can import other headers. Can declare:
```
Tokens           paint 0x0055ffff as primary
Components       card(w h color) ...
World functions  @stack_y gap ...
Signal bindings  signal pointer.over state raw.pointer.bounds_hit
Surfaces         surfaces { base float modal }
Font bindings    font Inter "/fonts/inter.woff2"
```

---

## 26. Two File Formats

```
.nd     Human-writable source. What you write. Version controlled.
        Readable unrendered. Edit in any text editor.

.ndb    Binary AST. What ships. What the server serves.
        What the browser fetches. What the renderer reads.
        No parsing overhead. Typed. Compressed.
```

Build tool (compile once per developer per build):
```
.nd source
  → validates all @references exist in scope
  → type-checks all property values
  → checks signal kind compatibility (pulse-only for on)
  → detects circular dependencies
  → detects side effects in world functions
  → resolves all imports
  → inlines pure parameterized nodes
  → compiles to binary AST
  → outputs .ndb

Errors are build errors. Never runtime failures.
Broken reference = caught before shipping. Always.
```

Client (runs trillions of times globally):
```
WASM renderer. One binary. Deployed once. Cached forever.
Same output on every device.
No browser rendering differences.
The renderer IS the spec.
```

**Performance characteristics:**

```
Memory per 1000 nodes    ~34 KB (fits in L1 cache)
DOM equivalent           300-600 KB (lives in RAM)
Improvement              10-20×

CPU per frame            one evaluation pass, dependency-ordered
DOM equivalent           3-5 tree walks per frame
Improvement              3-5×

Allocations per update   zero (signal graph pre-allocated at scene load)
DOM equivalent           dozens to hundreds per update
GC pressure              eliminated
```

---

## 27. Keyword Reference

**Complete keyword list — 16 keywords:**

```
NAVIGATION
  go           instruction to browser to navigate to URL

STATE
  var          reactive mutable state
  data         var whose value comes from outside
  task         data that you trigger via .run

CONTROL FLOW
  if           condition on existence (node level) or value (property level)
  else         alternate branch
  each         replication directive
  on           reaction to signal edge — only place mutation is valid
  return       value a node produces — exits evaluation immediately

CONTENT VERBS
  text         flowing markdown content
  paint        color instruction to renderer
  media        external content — any format, platform decodes

COMPONENT
  slot         content injection point in a component

STRUCTURAL
  import       brings header file declarations into scope
  global       persistent scope across navigation
  world        governing function for children — snapshot→compute→integrate
```

**Implicit contexts (never written as keywords):**

```
self       this node's geometric state
prev       previous sibling's geometric state
parent     parent node's geometric state
index      current loop position (inside each)
viewport   root viewport node
params     URL parameters (from viewport.path pattern)
children   child snapshot array (inside world functions)
world      result context (inside on world.result)
p          pixel coordinate (inside SDF expressions)
```

**Implicit runtime signals (always in scope):**

```
click      pulse — node activated
focus      state — node owns input routing
blur       pulse — focus leaves node
```

**Operators:**

```
+ - * / %
== != < > <= >=
and  or  not
? :
??
|
as
0x
```

---

## 28. What Doesn't Exist

```
Box model           no margin padding float display
CSS cascade         no inheritance no specificity no cascade
Z-index             surfaces handle layering
Font management     device's problem — name only
Media queries       self-width via parent.w or viewport.w
while loop          use signals or task
for(;;)             each covers all web UI cases
goto                obviously
Global namespace    explicit scope only
Runtime errors      build errors only — except world length mismatch
                    and frame budget exceeded
```

---

## 29. Open Problems

Formally flagged. Not papered over.

```
Document model      the hypertext web — articles, blogs, wikipedia
                    flowing text, hyperlinks, columns, footnotes, tables
                    the canvas model is wrong for documents
                    needs a dedicated design pass

Layout accumulation the world model's implementation detail
                    pure world function implementing stack needs
                    cumulative child heights — how does the runtime
                    expose accumulated state to world functions?
                    (assigned to DeepSeek branch)

.ndb format         binary AST structure — opcodes, byte layout,
                    streaming model — never formally defined

touch internals     touch[i].id for cross-frame finger tracking

Mixed unit math     int + float in expressions — build error for now
                    full resolution pending
```

---

## 30. Complete Example

```
import tokens.ndh
import surfaces.ndh
import sdf.ndh
import hid/pointer
import hid/scroll

global
  var current null
  data library from "/api/tracks"

@track(data)
  var data data
  size 1.0 56
  paint surface
  radius card_r

  if pointer.over
    paint raised

  media data.cover
    pos 0.0 0.5
    size 44 44
    fit cover

  text data.title
    pos 0.08 0.4
    type 14 600
    paint ink

  text data.artist
    pos 0.08 prev.b + 2
    type 12 400
    paint muted

  on click
    current = data

@page
  size 1.0 1.0
  paint bg

  @sidebar
    size 280 1.0
    pos 0.0 0.0
    paint surface

    text "Library"
      pos 0.5 0.06
      type 20 700
      paint ink

    if library.loading
      @spin
        pos 0.5 0.5
        smooth_union(circle(0.5, 0.5, 0.3), circle(0.3, 0.4, 0.15), 0.1)
        paint 0x0055ffff

    else if library.error
      text library.error.message ?? "Error."
        pos 0.5 0.5
        type 13 400
        paint muted

    else if not library.value
      text "No tracks."
        pos 0.5 0.5
        type 13 400
        paint muted

    else
      @list
        pos 0.0 0.12
        size 1.0 0.88
        scroll y
        var offset 0

        @_gap size 0.0 -8

        each library.value track
          @row @track(track)
            pos 0.0 prev.b + 8

  @main
    pos 0.28 0.0
    size parent.w - 280 1.0
    paint bg

    if not current
      text "Select a track."
        pos 0.5 0.5
        type 16 400
        paint muted

    else
      media current.cover
        pos 0.5 0.28
        size 220 220
        fit cover

      text current.title
        pos 0.5 0.48
        type 24 700
        paint ink

      text current.artist
        pos 0.5 prev.b + 6
        type 16 400
        paint muted
```

---

*End of .nd Language Specification v0.3*
*Built from first principles. Every keyword justified.*
*16 keywords. One primitive. One reading direction. One model.*
