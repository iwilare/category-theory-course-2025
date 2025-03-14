# 2025-02-21 - Lecture 4 (String and pasting diagrams)

## Today's plan

- Introduce some sound and complete graphical languages to talk about any category:

## Algebraic language for categories

The language of category theory that we have seen so far is an *algebraic* one.

An example of an algebraic expression:

$$
x*y*z * w * p = 3
$$

If we have a category, we can write something like this:

Assume $f : A \to B, g : B \to B, h : B \to C$, $k : A \to R, m: R \to C$:

$$f \,; g \,; g \,; \textsf{id}_a \,; h = k \,; m$$

The connection to algebra comes from the monoid examples that we saw in the previous lecture, where the composition symbol $;$ represents addition of numbers and you are literally adding numbers together ($id_a$ is zero.)

## Graphical language for categories - String diagrams

String diagrams are a way to represent graphically specific arrows of any category.

This is what a string diagram looks like:
https://arxiv.org/pdf/1803.05316

How to draw string diagrams out of some category $C$:
1. For every object `A` of my category, I can draw a wire that has the name `A` on top of it.
```
    A
--------
```
2. For every arrow symbol $f: A \to B$ I can draw a little box that joins $A$ and $B$ with the name $f$ inside of it:

```
 A   +-------+  B
-----|   f   |------
     +-------+
```
3. This is how you draw the expression `compose(f,g)`:

```
 A   +-------+  B  +-------+   D
-----|   f   |-----|   g   |------
     +-------+     +-------+
```

4. The identity arrow for some object $A$ is drawn like this:
```
    A
---------
```

In theory, you should/could draw it like this:

```
 A   +--------+  A
-----|  id_A  |------
     +--------+
```

---
---

This is how you draw the expression $h \,; \textsf{id}_C \,; k$,

i.e, `compose(compose(h, id[C]), k) = compose(h, k)` another one:

```
 A   +-------+        C        +-------+   D
-----|   h   |-----------------|   k   |------
     +-------+                 +-------+
```

So, the two expressions above, which are equal because of the laws of categories, actually become indistinguishable when drawn as string diagrams.


String diagrams automatically hide the fact that composition is associative, given $f:A \to B,g:B \to C,h:C \to D$

```
 A   +-------+  B  +-------+  C     C   +-------+   D
-----|   f   |-----|   g   |---     ----|   h   |------
     +-------+     +-------+            +-------+

                          =

 A   +-------+  B     B  +-------+   C  +-------+   D
-----|   f   |---     ---|   g   |------|   h   |------
     +-------+           +-------+      +-------+

                          =

 A   +-------+  B   +-------+   C  +-------+   D
-----|   f   |------|   g   |------|   h   |------
     +-------+      +-------+      +-------+

```



*Equalities in a string diagram are represented in time*: you can unambiguously only draw one diagram at a time; then you typically say that one and the one that you drew after are equal.

If I know that $g\,;h = i \,;p$, then I draw this:

```
   B  +-------+   C  +-------+   D
   ---|   g   |------|   h   |------
      +-------+      +-------+

                  =

   B  +-------+   E  +-------+   D
   ---|   i   |------|   p   |------
      +-------+      +-------+
```


So far, only "sequential composition" is allowed, i.e., I cannot put arrows in parallel because I have not told you how you can achieve this in terms of the structure of categories.

## Graphical language for categories - Pasting diagrams

Examples of pasting diagrams: https://www.google.com/search?q=commutative+diagrams

A website to draw pasting diagrams: https://q.uiver.app/

Pasting diagrams are precisely pieces of the drawings that we did in the past to represent the entire category: the only difference is that now we only take *some* objects, *some* arrows, and draw *some* equations with a small = symbol whenever it makes sense to do that. Moreover, we simply remove the dot below the label for the type (which you typically put in a graph).

So, the graph you obtain is this:
- *Nodes:* object/types,
- *Edges:* arrows/programs from one type to the other.

There's a subtle aspect to drawing these:
- The drawings we saw so far represent *entire* categories.
- *A pasting diagram simply shows you some arrows in your category, possibly also telling you that some equation holds.*

Another things is the fact that in a pasting diagram I can draw multiple arrows at the same time, this cannot be done with string diagrams.

Example of a pasting diagram, for some huge category $C$:

```
       f
  A ------> B
  |         |
h |         | g
  |         |
  v         v
  C ------> D
       i
```

When the equation $f \,; g = h \,; i$ we call this a "commutative diagram".
Commutativity of this diagram means that the equation $f = h \,; i$ holds.
```
  A
  | \
h |  \ f
  |   \
  v    v
  C ---> D
      i
```

Pasting and commutative diagrams can be distinguished in this way:

```
       f
  A ------> B
  |         |
h |    =    | g
  |         |
  v         v
  C ------> D
       i
```

When the equals is present it means that this is a *commutative* diagram, not a pasting one, i.e., it's just telling that the equation $f \,; g = h \,; i$ is true.

```
       f         k        l
  A ------> E ------> O ----> B
  |                          |
h |             =            | g
  |                          |
  v                          v
  C -----------------------> D
              i
```

Equalities between calls of `compose` are represented by the fact that you might have multiple paths that start and end in the same place, and these give you the same result.

Pasting is done like this:

```
       f         k        l
  A ------> E ------> O ----> B     B ---------+
  |                          |      |          | m
h |             =            | g  g |    =     |
  |                          |      |          |
  v                          v      v          v
  C -----------------------> D      D -------> Z
              i                           p


       f         k        l
  A ------> E ------> O ----> B ---------+
  |                          |           | m
h |             =            | g    =    |
  |                          |           |
  v                          v           v
  C -----------------------> D --------> Z
              i                     p
```

- Equation in the first diagram: $f \,; k \,; l \,; g = h \,; i$
- Equation in the second diagram: $m = g \,; p$
- Then this one also holds: $f \,; k \,; l \,; m $ = $f \,; k \,; l \,; g \,; p$ = $h \,; i \,; p $

How did I derive these? Simply by substituting equal things for other equal things.

*Equalities in a pasting diagram are represented in space*: if you say that a diagram commutes, the compositions from every possible start and every possible end are indeed equal (i.e., the calls to the compose function are equal).

## Term language for categories

At the same time you have programs. Given *ANY* category whatsoever, one can construct a *canonical* corresponding programming language:

- *(Types).* type of your programming language are exactly the objects of the category.
- *(Programs/functions).* are the arrows.

```rust
// This looks like Rust, but it's really a new programming language that was created from thin air from the data of the category that you gave me!
fn program1(a: A) -> B {
    f(g(a))
}
fn program2(a: A) -> C {
    h(k(z(a)))
}
```

---
---


This means that whatever equation you can derive using string diagrams you can derive using the algebraic language (which is, really, the only one that exists and has been defined formally.)

### Theorem (partly by Joyal and Street ~1970, partly by other people, starting from Lambek ~1970). Reasoning with 1. string diagrams, 2.  pasting diagrams, 3. the term language and program equivalence, are all equivalent/equiexpressive.
