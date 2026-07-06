## What Belongs in the Core

The most dillematic thing I faced while designing this language was what stays in the core and what doesn't. Many things felt like they're obvious to a UI language, but could be reduced  to existing features of the language. The reasoning that helped nd bravely reject them follows such:

The machine, turing complete bunch of transistors is physics. It obeys its own rules whether we touch it or not. To make it do anything we want, we build layers of abstraction on top of it.

Abstraction Layer 1 sits on the hardware. It's repetitive and boilerplate heavy as a consequence of having to describe everything from nothing in the hardware's own terms.

Abstraction Layer 2 is where primitives live. A primitive is an irreducible operation up to the machine.

Abstraction Layer 3 is composition. A composition of primitives.

*paint is a primitive* because it terminates at the GPU. It takes a 32-bit integer, hits the hardware and becomes a pixel color. There's nothing underneath it that the language can expose without becoming the hardware itself.

*rotate is a composition* of primitives. It's sine, cosine, and matrix multiplication arranged in a specific convention. It can be reduced down to primitives, and the primitives so forth.

Appreciate math, it singlehandedly reduced countless primitives that nd bravely embraced. 

The core of this language only provides you with the primitives. The Standard Library, and other community led libraries will contain the composition.