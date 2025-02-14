# 2025-02-13 - Lecture 2 (Other categories, diagrams, string diagrams)

## Today's plan

- We will see other examples of categories
- Introduce some sound and complete graphical languages to talk about any category:

## The category $\textsf{Prog}$

- *(Objects.)* One object symbol for each possible type you can write in Rust.
- *(Arrows.)* One arrow symbol from `A` to `B` for each possible program, with a single argument, that can be written in Rust
- *(Identities.)* We pick the arrow
- *(Composition.)* Concatenation of paths, i.e., list concatenations

## The free category on a multi-edged graph

There are also some recipes which, given some data that you can pick, it constructs a new category for you.

- Idea behind free categories: *Given a graph, construct the free category.*

The idea of "free" in this context is that *there are no other equations other than those necessary to satisfy the category laws.*

- *(Objects.)* Exactly the nodes of the graph.
- *(Arrows.)* Lists of edges from the graph.
- *(Identities.)*
- *(Composition.)* Concatenation of paths, i.e., list concatenations

# Other examples of categories

Unfortunately, Rust is not expressive enough for us to capture a lot of examples which are standard in math (they would be extremely clunky).

Some examples where objects are types in Rust:

| **Name**     | **Objects**                           | **Arrows**                                                   |
|--------------|---------------------------------------|--------------------------------------------------------------|
| $\textsf{Prog}$  | Types                              | *Rust programs*                                              |
| $\textsf{Fin}$  | Finite types                       | *Rust programs*                                              |
| $\textsf{Par}$  | Types                              | *Rust programs which can crash (i.e., partial)*              |
| $\textsf{Rel}$  | Types                              | *Relations*, i.e., programs `fn rel(a: A, b: B) ->  bool`    |
| $\textsf{Perm}$ | Types                              | *Invertible Rust programs*                                   |
| $\textsf{Pointed}$  | Pointed types                      | *Programs preserving the point*                              |
| $\textsf{Lin}$  | Resources (Linear types)           | *Programs which do not duplicate nor destroy resources*      |
| $\textsf{Prob}$  | Types                              | *Probability distsributions*  `fn rel(a: A, b: B) ->  f32`   |

Typical examples from math:

| **Name**     | **Objects**                               | **Arrows**                                                       |
|--------------|-------------------------------------------|------------------------------------------------------------------|
| $\textsf{Mon}$  | Monoids                                | *Monoid homomorphisms*                                           |
| $\textsf{Rng}$  | Rings                                  | *Ring homomorphisms*                                             |
| $\textsf{Grp}$  | Groups                                 | *Group homomorphisms*                                            |
| $\textsf{Top}$  | Topological spaces                     | *Continuous functions*                                           |
| $\textsf{Mat}$  | Natural numbers                        | *Matrices* $M_{n,m}$ with $n$ rows and $m$ columns               |
| $\textsf{Vect}$ | Vector spaces                          | *Linear functions*                                               |
| $\textsf{Hilb}$ | Complete inner product vector spaces   | *Bounded linear maps*                                            |

Typical examples from the intersection of category theory and computer science:

| **Name**     | **Objects**                               | **Arrows**                                                       |
|--------------|-------------------------------------------|------------------------------------------------------------------|
| $\textsf{Cat}$  | Categories                             | *Functors*                                                       |
| $\textsf{Pre}$  | Preorders                              | *Monotone functions*                                             |
| $\omega\textsf{CPO}_{\bot}$  | $\omega$-chain complete partial orders with $\bot$ | *Monotone functions preserving least upper bounds and $\bot$*               |
| $[\mathbb C,\mathbb D]$       | Functors $\mathbb C \to \mathbb D$                   | *Natural transformations*            |
| $\textsf{PER}$ | Partial equivalence relations on ℕ     | *A function $f : \N \to \N$ with a natural number which realizes $f$ as a primitive recursive function* |

# Preorders

We saw that a category with one object is the same thing as a monoid.

What if the *one value* part was on the side of morphisms instead of objects?

## Definition: preorder

Idea: two types `A`, `B` are connected when the type `Arr[a,b]` has one element. They are not connected when the type is empty. We do not care *how* they are connected (in categories, we care about which program we run! Not all program symbols from one type to the other are the same.)

1. *(Objects).* A choice of a Rust type `Obj`;
2. *(Arrows).* For every value `a:Obj`, `b:Obj`, a Rust type *which MUST have either zero or one elements*;
3. *(Identities).* For every value `a:Obj`, there is an element `id[a]: Arr[a,a]`
4. *(Composition).* For every value `a:Obj`, `b:Obj`, `c:Obj`, a Rust function
```rust
fn compose[a][b][c](a: Arr[a,b], g: Arr[b,c]) -> Arr[a,c] {
    ...
    // match a {
    //     One =>
    //         match b {
    //             One => One
    //         }
    // }
}
// The idea is that asking that a composition map exists is the same as saying that the following condition holds:
//
//     *If both Arr[a,b] and Arr[b,c] have an element, then Arr[a,c] must have an element too.*
```
Subject to the following laws:
- *(Left identity).* Given a value `f: Arr[a,b]` for some `a:Obj,b:Obj`,
    ```rust
    compose[a][b][b](f, id[b]) = f
    ```
    This always holds!
5. *(Left unitality.)* Given a value `f: Arr[a,b]` for some `a:Obj,b:Obj`,
    ```rust
    compose[a][a][b](id[a], f) = f
    ```
    This always holds!
7. *(Associativity.)* For every value `a,b,c,d: Obj`, and every value `f:Arr[a,b], g:Arr[b,c], h:Arr[c,d]`


## Example: the preoder of cities reachable by a flight

- *(Objects).* A type with names of cities.
- *(Arrows).* A type with one element when a city can be reached by a series of Finnair flights.
- *(Identities.)* Staying in the same city counts as a series of flights.
- *(Composition).*

# Categories where one picks a different notion of equality

The category of programs can be thought of in many different ways.

- Programs under syntactic equivalence: two program symbols are equal when they have the same source code.
- Programs under temporal equivalence: two program symbols are equal when they have the
- Programs under behavioural equivalence: two programs symbols are

Equality

- Trips for the free graph, trips under the equality "have the same price".
- Trips for the free graph, trips under the equality "take the same length".

# Categories as languages

Example of an equation between programs (e.g., in Rust):

## Algebraic language for categories

If we have a category, we can write something like this:

Assume $f : A \to B, g : B \to B, h : B \to C$, $k : A \to R, m: R \to C$:

$$f \,; g \,; g \,; \textsf{id}_a \,; h = k \,; m$$

One says that this is an "algebraic" perspective on categories. The connection to algebra comes from the monoid examples that we saw in the previous lecture, where the composition symbol $;$ represents addition of numbers and you are literally adding numbers together ($id_a$ is zero.)

## Graphical language for categories - Pasting diagrams

Examples of pasting diagrams: https://www.google.com/search?q=commutative+diagrams

A website to draw pasting diagrams: https://q.uiver.app/

Pasting diagrams are precisely pieces of the drawings that we did in the past to represent the entire category: the only difference is that now we only take *some* objects, *some* arrows, and draw *some* equations with a small = symbol whenever it makes sense to do that. Moreover, we simply remove the dot below the label for the type (which you typically put in a graph).

So, the graph you obtain is this:
- *Nodes:* types,
- *Edges:* programs from one type to the other.

Equalities between calls of `compose` are represented by the fact that you might have multiple paths that start and end in the same place.

Example:

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

If `compose(f,g) = compose(h,i)` we say that the diagram "commutes" (stupid name). And we write it like this:

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

*Equalities in a pasting diagram are represented in space*: if you say that a diagram commutes, the compositions from every possible start and every possible end are indeed equal (i.e., the calls to the compose function are equal).

## Graphical language for categories - String diagrams

This is what a string diagram looks like:
https://arxiv.org/pdf/1803.05316

This is how you draw the expression `compose(f,g)`:

```
 A   +-------+  B  +-------+   D
-----|   f   |-----|   g   |------
     +-------+     +-------+
```

This is how you draw the expression `compose(h, id[C]), compose(g)` another one:

```
 A   +-------+        C        +-------+   D
-----|   h   |-----------------|   k   |------
     +-------+                 +-------+
```

This is how you draw the identity on the object `A`:

```
        A
-----------------
```

String diagrams automatically hide the fact that composition is associative, given $f:A \to B,g:B \to C,h:C \to D$

```
 A   +-------+  B  +-------+  C     C  +-------+   D
-----|   f   |-----|   g   |---     ----|   h   |------
     +-------+     +-------+           +-------+

                          =

 A   +-------+  B     B  +-------+   C  +-------+   D
-----|   f   |---     ---|   g   |------|   h   |------
     +-------+           +-------+      +-------+

                          =

 A   +-------+  B   +-------+   C  +-------+   D
-----|   f   |------|   g   |------|   h   |------
     +-------+      +-------+      +-------+

```


*Equalities in a commutative diagram are represented in time*: you can unambiguously only draw one diagram at a time; then you typically say that one and the one that you drew after are equal.

## Term language for categories
At the same time you have programs. Given *ANY* category whatsoever, one can construct a *canonical* corresponding programming language:

- *(Types).* type of of your prorgamming language are exactly the types of the category.
- *(Programs).* are the arrows.

```rust
// This looks like Rust, but it's really a new programming language that was created from thin air from the data of the category that you gave me!
fn program1(a: A) {
    f(g(h))
}
fn program2(a: A) {
    h(k(h))
}
```

- We must have that a certain sequence of program




### Theorem. Reasoning with string diagrams, reasoning with pasting diagrams, reasoning with the term language and program equivalence, and reasoning with are all equivalent/equiexpressive.

This means that whatever equation you can derive using string diagrams you can derive using the algebraic language (which is, really, the only one that exists and has been defined formally.)

# Functors

Idea behind functors: functors embody the idea of interpreting types and programs of a programming language into another category.

Given two categories, $C,D$, a functor $F$ amounts to the following choices:
- *(Function on objects)* a choice of a Rust program `F_obj` with the following signature:
    ```rust
    fn F_obj(a: C.Obj): D.Obj {
        ...
    }
    ```
- *(Function on arrows)* for every value `a:C.Obj`, `b:C.Obj` a choice of a Rust program `F_arrows[a][b]`
    ```rust
    fn F_arrows[a][b](f: C.Arr[a,b]) -> D.Arr[F_obj(a), F_obj(b)] {
        ...
    }
    ```

- *(`F_arrows` respects identities)*: for every `a:C.Obj`, we know that thers is a value `id[a]:C.Arr[a,a]`. Calling `F_arrows[a][a]` on that value must give you exactly the identity in the corresponding type `D.Arr[F_obj(a), F_obj(a)]`, i.e., this equation holds:
    ```rust
    F_arrows[a][a](C.id[a]) = D.id[F_obj(a)]
    ```

- *(`F_arrows` respects compositions)*: for every `a,b,c:C.Obj` and every `f:C.Arr[a,b]`, `g:Arr[b,c]`, this equation holds:
    ```rust
    F_arrows[a][b](C.compose(f, g)) =
    D.compose(F_arrows[a][b](f), F_arrows[b][c](g))
    ```

## Notation

Again, we can often omit the indices if we know the types of the arrows involved.

| Math notation | Rust notation |
|---|---|
| $F(a)$ | `F_obj(a)` |
| $F(f)$ | `F_arrows(f)` |
