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

### Below are the guidelines for the string diagrammatic language of monoidal categories.

1. *How to draw*: objects $X$? They are drawn as wires with a label on top
   ```
      X
   -------
   ```
2. *How to draw*: an arrow $f : X \to Y$? As a box:
    ```
      X   +-------+   Y
    ------|   f   |-------
          +-------+
    ```
3. *How to draw*: the composition $f\,;g : X \to Z$ of an arrow $f : X \to Y$ with $g : Y \to Z$? By putting it in sequence:
    ```
      X   +---------+   Z
    ------|   f;g   |-------
          +---------+
    ```
    ```
      X   +-------+   Y   +-------+   Z
    ------|   f   |-------|   g   |-------
          +-------+       +-------+
    ```
    The fact that composition is associative ensure non-ambiguousness of composition graphically.
4. *How to draw*: identities $\textsf{id}_X$: we do not draw the box "$\textsf{id}_X$", we simply do the wire only.
    ```
      X   +--------+   X
    ------|  id_X  |-------
          +--------+
    ```
    ```
      X                X
    -----------------------

    ```

    The fact that wires can be "stretched" graphically (i.e., you add identities ) is justified in the theory by the fact that $\textsf{id}\,;f = f = f\,;\textsf{id}$.


So far, so good. This was already introduced in Lecture 4 on string diagrams.

Monoidal categories add these into the mix: since $\otimes : C \times C \Rightarrow C$ is a functor, I represent both its program on arrows and its program on objects.

1. *How to draw:* the program on objects of $\otimes$. Given two objects $X,Y$ in $C$, this is how I draw the object $X \otimes Y$ of $C$:
   ```
        X⊗Y
   --------------
   ```
   or

   ```
         X
   --------------

         Y
   --------------
   ```
    by putting wires *in parallel*.
2. *How to draw:* the program on arrows of $\otimes$. Given two arrows $f : X \to Y$ and $g : Z \to W$, this is how I draw the arrow $f \otimes g : X \otimes Z \to Y \otimes W$:

    ```
      X   +-------+   Y
    ------|   f   |-------
          +-------+

      Z   +-------+   W
    ------|   g   |-------
          +-------+
    ```
    by putting boxes *in parallel*. The actual arrow is this one:
    ```
      X   +-------+   Y
    ------|       |-------
          | f⊗g  |
      Z   |       |   W
    ------|       |-------
          +-------+
    ```
    or, since we represent the tensor of objects as wires in parallel,
    ```
     X⊗Z +--------+ Y⊗W
    ------|  f⊗g  |------
          +--------+
    ```
3. The *(Strict object associativity)* condition of strict monoidal categories ensures non-ambiguousness of putting wires in parallel:

    ```
      X
    ------
      Y
    ------
      Z
    ------
    ```
    is the same as


    ```
      X⊗Y
    --------
       Z
    --------
    ```
   is the same as

    ```
      (X⊗Y)⊗Z
    -------------
    ```
    or, going the other way, the first is the same as

    ```
       X
    --------
      Y⊗Z
    --------
    ```
   which is the same as

    ```
      X⊗(Y⊗Z)
    -------------
    ```

    Similarly, they are the same as `X⊗(Y⊗Z)`: **they are equal in the graphical representation so the category theory should also not distinguish them!**
4. The *(Arrow associativity)* condition of strict monoidal categories ensures non-ambiguousness of putting wires in parallel:


    ```
      X   +-------+   Y
    ------|   f   |-------
          +-------+

      Z   +-------+   W
    ------|   g   |-------
          +-------+

      Q   +-------+   H
    ------|   h   |-------
          +-------+
    ```

    they can be thought of as being put in parallel as $(f\otimes g) \otimes h$ or $f \otimes (g \otimes h)$: graphically they are indistinguishable, so this condition says precisely that the theory considers these two ways of putting stuff in parallel as equal.
4. *How to draw*: the unit object. *Answer:* we simply do not draw it.
    ```
           I
        - - - -

           A
        -------
    ```
    is the same as just
    ```
           A
        -------
    ```
    why? Because $I \otimes A = A = A \otimes I$. So, intuitively, $I$ acts as "empty space", which I can add or remove freely.
4. *How to draw*: the identity arrow on $I$. *Answer:* we simply do not draw it.
    ```
          I            I
        - - - - - - - - -
    ```
    is the same as just drawing nothing at all.
    ```



    ```
    why? Because $\textsf{id}_I \otimes f = f = f \otimes \textsf{id}_I$:

    ```
          I               I
        - - - - - - - - - - -

          X   +-------+   Y
        ------|   f   |-------
              +-------+

          I               I
        - - - - - - - - - - -
    ```
    is the same as just drawing nothing at all.
    ```



          X   +-------+   Y
        ------|   f   |-------
              +-------+


    ```
    Of course this also works on a single side:
    ```





          X   +-------+   Y = Y ⊗ I
        ------|   f   |----------------
              +-------+
    ```
    so I add useless "space on the top".

4. The *(Arrow associativity)* condition of strict monoidal categories ensures non-ambiguousness of putting wires in parallel:


    ```
      X   +-------+   Y
    ------|   f   |-------
          +-------+

      Z   +-------+   W
    ------|   g   |-------
          +-------+

      Q   +-------+   H
    ------|   h   |-------
          +-------+
    ```

    they can be thought of as being put in parallel as $(f\otimes g) \otimes h$ or $f \otimes (g \otimes h)$: graphically they are indistinguishable, so this condition says precisely that the theory considers these two ways of putting stuff in parallel as equal.
5. Functoriality of $\otimes$ is crucial, and corresponds with the following non-ambiguousness interpretation of string diagrams:

   Functoriality of $\otimes$: for every $f : A \to B$, $f' : B \to C$, $g : X \to Y$, $g' : Y \to Z$,$$(f \otimes g) \,; (f' \otimes g') = (f \,; f') \otimes (g \,; g')$$
    Either I put $f,g$ and $f',g'$ in parallel and then compose them sequentially,

     ```
         + - - - - - +    + - - - - - +

        A| +-------+ | B  | +-------+ | C
     ------|   f   |--------|   f'  |-------
         | +-------+ |    | +-------+ |

        X| +-------+ | Y  | +-------+ | Z
     ------|   g   |--------|   g'  |-------
         | +-------+ |    | +-------+ |

         + - - - - - +    + - - - - - +
     ```

     Or I can put in sequence $f,f'$ and $g,g'$ and then put these two in parallel.

     ```
         + - - - - - - - - - - - - - - +
        A| +-------+   B    +-------+  |C
     ------|   f   |--------|   f'  |-------
         | +-------+        +-------+  |
         + - - - - - - - - - - - - - - +

         + - - - - - - - - - - - - - - +
        X| +-------+   Y    +-------+  |Z
     ------|   g   |--------|   g'  |-------
         | +-------+        +-------+  |
         + - - - - - - - - - - - - - - +
     ```

     In either case, the graphical diagram without the dashed lines, which are like "brackets" is the same!

     ```
        A  +-------+   B    +-------+   C
     ------|   f   |--------|   f'  |-------
           +-------+        +-------+

        X  +-------+   Y    +-------+   Z
     ------|   g   |--------|   g'  |-------
           +-------+        +-------+
     ```

     So we ask that this equation is true in the theory.

# An example of an arrow for monoidal category

### Page 4 and 52 of https://arxiv.org/pdf/1803.05316 (note, we have not talked about enrichment in this course.)

Assume that
- $g : D \to E$
- $f : A \to B \otimes C$
- $r : C \otimes E \to G \otimes L$
- $k : H \otimes G \to M$
- $e : B \to H$

Then, how do I represent this as an arrow in my category, i.e., in the formal, precise, theory?

```

              +-----+  B        +-----+        H  +-----+
              |     |-----------|  e  |-----------|     |    M
 A            |  f  |           +-----+        G  |  k  |-----
--------------|     |------                  -----|     |
              +-----+  C   \                /     +-----+
                            \   +-----+ G  /
                             ---|     |----
 D +-----+  E                   |  r  |                      L
---|  g  |----------------------|     |-----------------------
   +-----+                      +-----+


```

First, I splice the diagram into horizontal segments:


```

               |    +-----+  B  |      B  +-----+  H       |   H  +-----+
               |    |     |---- | --------|  e  |--------- | -----|     |    M
 A             |  A |  f  |     |         +-----+          |   G  |  k  |-----
-------------  | ---|     |---- | ---                  --- | -----|     |
               |    +-----+  C  | C  \                /    |      +-----+
               |                |     \   +-----+ G  /     |
               |                |      ---|     |----      |
 D +-----+  E  |        E       |  E      |  r  |          |                 L
---|  g  |---  | -------------- | --------|     |--------- | -----------------
   +-----+     |                |         +-----+          |


```

Then I splice each segment into vertical ones (using underscores for the vertical splits)

```

               |    +-----+  B  |       B  +-----+  H      |   H  +-----+
               |    |     |---- | ---------|  e  |---------| -----|     |
               |    |     |     |          +-----+         |      |     |    M
               |    |  f  |     +__________________________+      |  k  |-----
 A             |  A |     |     |                          |   G  |     |
-------------  | ---|     |---- | ---                  --- | -----|     |
               |    +-----+  C  | C  \                /    |      +-----+
               |                |     \   +-----+ G  /     +___________________
               +________________+      ---|     |----      |
 D +-----+  E  |        E       |         |  r  |          |                 L
---|  g  |---  | -------------- | --------|     |--------- | -----------------
   +-----+     |                |         +-----+          |


```

Then, you use $;$ for horizontal "sequential" compositions and the program on arrows of $\otimes$ on the vertical splits.

```

               |    +-----+  B  |       B  +-----+  H      |   H  +-----+
               |    |     |---- | ---------|  e  |---------| -----|     |
               |    |     |     |          +-----+         |      |     |    M
               |    |  f  |     +__________________________+      |  k  |-----
 A             |  A |     |     |                          |   G  |     |
-------------  | ---|     |---- | ---                  --- | -----|     |
               |    +-----+  C  | C  \                /    |      +-----+
               |                |     \   +-----+ G  /     +___________________
               +________________+      ---|     |----      |
 D +-----+  E  |        E       |         |  r  |          |                 L
---|  g  |---  | -------------- | --------|     |--------- | -----------------
   +-----+     |                |         +-----+          |

   id_A ⊗ g        f ⊗ id_E             e ⊗ r                  k ⊗ id_L

```
The final arrow is:

$$
(\textsf{id}_A \otimes g)\,;(f \otimes \textsf{id}_E)\,;(e \otimes r)\,;(k \otimes \textsf{id}_L).
$$

Note! If I slightly weak graphically the diagram and then I do this "splicing" algorithm again, I obtain a different (in principle) arrow!

Imagine for example that I move $e$ a little bit more to the left:

```

               |    +-----+  B  |       B  +-----+  H       |           H              |   H  +-----+
               |    |     |---- | ---------|  e  |--------- | ------------------------ | -----|     |    M
 A             |  A |  f  |     |          +-----+          |                          |   G  |  k  |-----
-------------  | ---|     |---- | ------------------------- | ---                  --- | -----|     |
               |    +-----+  C  |   C                       | C  \                /    |      +-----+
               |                |                           |     \   +-----+ G  /     |
               |                |                           |      ---|     |----      |
 D +-----+  E  |        E       |   E                       |  E      |  r  |       L  |                 L
---|  g  |---  | -------------- | ------------------------- | --------|     |--------- | -----------------
   +-----+     |                |                           |         +-----+          |

   id_A ⊗ g        f ⊗ id_E         e ⊗ id_C ⊗ id_E                 id_H ⊗ r               k ⊗ id_L
```

$$
(\textsf{id}_A \otimes g )\,;(f \otimes \textsf{id}_E)\,;(e \otimes \textsf{id}_C \otimes \textsf{id}_E)\,;(\textsf{id}_H \otimes r)\,;(k \otimes \textsf{id}_L).
$$

Or I could even shift it to the right:

```

               |    +-----+  B  |           H              |       B  +-----+  H         |   H  +-----+
               |    |     |---- | ------------------------ | ---------|  e  |---------   | -----|     |    M
 A             |  A |  f  |     |                          |          +-----+            |   G  |  k  |-----
-------------  | ---|     |---- | ---                  --- | -------------------------   | -----|     |
               |    +-----+  C  | C  \                /    |   G                         |      +-----+
               |                |     \   +-----+ F  /     |                             |
               |                |      ---|     |----      |                             |
 D +-----+  E  |        E       |  E      |  r  |      L   |   L                         |                 L
---|  g  |---  | -------------- | --------|     |--------- | -------------------------   | -----------------
   +-----+     |                |         +-----+          |                             |

   id_A ⊗ g        f ⊗ id_E           id_H ⊗ r                 e ⊗ id_G ⊗ id_L             k ⊗ id_L
```

$$
(\textsf{id}_A \otimes g )\,;(f \otimes \textsf{id}_E)\,;(\textsf{id}_H \otimes r) \,; (e \otimes \textsf{id}_G \otimes \textsf{id}_L) (k \otimes \textsf{id}_L).
$$

The question is whether these two (in principle different) arrows are the same.

The answer is yes! You can use the interchange law to prove this manually. But, more in general, the answer is even better: this works for *every* possible small tweak that you can do with these diagrams:

### Theorem (Joyal and Street 1991). The intuitive graphical manipulations one can do with string diagrams (e.g., sliding things around, increasing/decreasing the length of wires, as long as you don't break the geometry) always produces arrows which are equal under the algebraic equations of (strict) monoidal categories.

This means you can use a nice graphical language in a fully equivalent way to work with the mathematical model of monoidal categories.

# Strict symmetric monoidal categories

A strict monoidal category is said to be symmetric if it comes equipped with a choice of arrow

$$\textsf{swap}_{X,Y} : X \otimes Y \to Y \otimes X$$

for every object $X,Y$ which is
1. Natural: for every arrow $f : A \to A'$, $g : B \to B'$ these two arrows $A \otimes B \to B' \otimes A'$ are the same:

$$(f \otimes g) \,; \textsf{swap}_{A',B'} = \textsf{swap}_{A,B} \,; (g \otimes f)$$

2. Symmetricity: $\textsf{swap}_{Y,X}$ is the inverse map to $\textsf{swap}_{X,Y}$.

Note! The swap map is, again, not unique. Of course its inverse is unique.

Graphically, we will represent $\textsf{swap}_{X,Y}$ as this arrow:

```
 X                Y
--------     --------
        \   /
         \ /
          X
         / \
 Y      /   \     X
--------     --------
```

instead of

```
 X  +------------+  Y
----|            |----
    |            |
    |            |
    | swap_{X,Y} |
    |            |
 Y  |            |  X
----|            |----
    +------------+
```

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
