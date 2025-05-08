# 2025-05-02 - Lecture 19 (Uniqueness, monoidal categories, string diagrams)

# Interlude: uniqueness of stuff.

Today's topic is pretty light so we'll sprinkle in there some interesting stuff.

## Definition: inverse arrow

Given objects $X,Y$ and an arrow $f : X \to Y$, an arrow $g : Y \to X$ is said to be *the* inverse of $f$ if these familiar equations are true:
$$
f\,;g = \textsf{id}_X \quad g \,; f = \textsf{id}_Y
$$
a choice of inverse for $f$ means that $X \cong Y$.

We denote the inverse of some arrow $f : X \to Y$ with the notation $f^{-1} : Y \to X$ when it exists.

$$
f\,;f^{-1} = \textsf{id}_X \quad f^{-1} \,; f = \textsf{id}_Y
$$

## Example: isomorphisms are not unique

Either two objects are isomorphic or they are not... but the isomorphisms themselves are not unique.

Example: take the category $\textsf{Prog}$, the object $\textsf{Bool}$ and this object $\textsf{State}$:
```rust
enum Bool {
  True, False
}
enum State {
  Pass, Deny
}
```
of course `Bool` $\cong$ `State`, but there are two possible ways of proving this:

```rust
fn b2s(b: Bool) -> State {
  if b { Pass } else { Deny }
}
fn s2b(b: State) -> Bool {
  match b {
    Pass => True,
    Deny => False
  }
}
```

Of course these two are inverses, so `Bool` $\cong$ `State`.

However, there is also this possibility:

```rust
fn b2s_other(b: Bool) -> State {
  if b { Deny } else { Pass }
}
fn s2b_other(b: State) -> Bool {
  match b {
    Pass => False,
    Deny => True
  }
}
```

Again, these two are obviously inverses of each other, so we have proven that `Bool` $\cong$ `State` again, but in a different way.

Notice that `b2s` $\not =$ `b2s_other` are not the same arrow (in the category theoretic sense) because they are not program equivalent.

*(Takeaway message.)* So, if someone claims that $A \cong B$ using two arrows $f : A \to B, f' : B \to A$ and somebody else also claims that $A \cong B$ having used two arrows $g : A \to B, g' : B \to A$, we cannot conclude that $f = g$ nor that $f' = g'$.

# Theorem: inverse arrows are unique.

Given an arrow $f : X \to Y$, there is only one possible arrow $g : Y \to X$ that satisfies the property of being an inverse of $f$ (this means that we can use the notation $f^{-1}$ unambiguously, there is only one possible arrow with this property).

*Proof.*

Imagine that I have another $g' : Y \to X$ with the property of being an inverse of $f$.

(This means that $g'\,;f = \textsf{id}_Y$ and that $f\,;g' = \textsf{id}_X$).

Then I want to show that $g' = g$ all along.

$$\begin{array}{rll}
  & g & \text{(identity law)} \\
= & g \,; \textsf{id}_X & \text{($g'$ is an inverse for $f$)} \\
= & g \,; (f \,; g') & \text{(associativity)} \\
= & (g \,; f) \,; g' & \text{($g$ is an inverse of $f$)} \\
= & \textsf{id}_Y \,; g' & \text{(identity law)} \\
= & g' \\
\end{array}$$

# Theorem: identity arrows are unique.

Someone makes all the choice of a category $C$, choosing for every $X$ a value $\textsf{id}_X : X \to X$ in the type of arrows that they have picked.

Someone else comes along and makes all the same choices but claiming that they picked a different identity arrow $\textsf{id}'_X : X \to X$ in the type of arrows.

Let's show that, if both of them really did prove all the identity laws, then they must have picked the same value in the type of arrows `Arr_X_X` for every object `X`, and $\textsf{id} = \textsf{id}'$.

*Proof*.

For every object $X$,

$$\begin{array}{rll}
  & \textsf{id}_X & \text{(right identity law of $\textsf{id}'_X$ for $f := \textsf{id}'_X$)} \\
= & \textsf{id}_X \,; \textsf{id}'_X & \text{(left identity law of $\textsf{id}_X$ for $f := \textsf{id}_X$)}  \\
= & \textsf{id}_X' & \\
\end{array}$$

# Theorem: having chosen $\otimes$, the unit object $I$ is unique.

Same thing but for combine operations and unit objects in a strict monoidal category.

$$\begin{array}{rll}
  & I & \text{(right identity on objects of $I$ for $X := I'$)} \\
= & I \otimes I' & \text{(left identity on objects of $I'$ for $X := I$)}  \\
= & I' & \\
\end{array}$$


---
---

*Change of topic.*

---
---

# String diagrams for monoidal categories.

- Combine objects: you put objects on "top" of each other graphically.
- Combine arrows: you put arrows on "top" of each other graphically.
- Associativity of combining arrows: three wires on top of each other.
- Functoriality of $\otimes$, which amounts to the following equation:
  $$(f \otimes g) \,; (f' \otimes g') = (f \,; f') \otimes (g \,; g')$$
  this is a sort of "coherence"/"non-ambiguousness" of interpretation.
  Graphically: two parallel wires, four boxes, two on each wire.

# Strict symmetric monoidal categories

A strict monoidal category is said to be symmetric if it comes equipped with a choice of arrow

$$\textsf{swap}_{X,Y} : X \otimes Y \to Y \otimes X$$

for every object $X,Y$ which is
1. Natural: for every arrow $f : A \to A'$, $g : B \to B'$ these two arrows $A \otimes B \to B' \otimes A'$ are the same:

$$(f \otimes g) \,; \textsf{swap}_{A',B'} = \textsf{swap}_{A,B} \,; (g \otimes f)$$

2. Symmetricity: $\textsf{swap}_{Y,X}$ is the inverse map to $\textsf{swap}_{X,Y}$.


Note! The swap map is, again, not unique. Of course its inverse is unique.

---

*Cool small extra.*

There are some interesting cases when condition 2. fails, and it can be replaced with this weaker notion:

2b.  $\textsf{swap}_{X,Y}$ Has an inverse map
$$\textsf{swap}_{X,Y} : Y \otimes X \to X \otimes Y$$
   which is also natural in the same way as above.

(but, in principle, $\textsf{unswap}$ and $\textsf{swap}$ might be completely different/unrelatd families of arrows!)

 people call this notion "strict *braided* monoidal category":

**Insert drawing here.**

# Example: $(\N,\le)$ and its monoidal structures on top

Both of the monoidal structures we can put on $(\N,\le)$ are symmetric:

$$a * b = b * a$$

$$a + b = b + a$$

# Example: any $C$ with products and terminal $(C,\times,1)$

*(Usual strification caveat here)*.

- Instance: $(\text{Cat},\times,1)$
- Instance: $(\text{Prog},\times,1)$

# Non-example: $([C,C],{\,;\,},\textsf{id}_C)$

This is typically very much not symmetric. Take
- $C := \text{Prog}$,
- $\textsf{List} : \text{Prog} \to \text{Prog}$,
- $\textsf{Maybe} : \text{Prog} \to \text{Prog}$,

Then $\textsf{List} \otimes \textsf{Maybe}$ is the functor sending the type `X` $\mapsto$ `Maybe<List<X>>`.

Then $\textsf{Maybe} \otimes \textsf{List}$ is the functor sending the type `X` $\mapsto$ `List<Maybe<X>>`.

But these two functors are very different! Even less they are literally the same object.
