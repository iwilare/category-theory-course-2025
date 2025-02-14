# 2025-02-13 - Lecture 2 (Other categories, diagrams, string diagrams)

## Today's plan

- We will see other examples of categories
- Introduce some sound and complete graphical languages to talk about any category:

# Other examples of categories

Unfortunately, Rust is not expressive enough for us to capture a lot of examples which are standard in math (they would be extremely clunky).
Some examples:

| **Name**     | **Objects**                             | **Arrows**                                                       |
|--------------|---------------------------------------|------------------------------------------------------------------|
| $\textsf{Typ}$  | Types                                  | *Rust programs*                                                  |
| $\textsf{Par}$  | Types                                  | *Rust programs which can crash (i.e., partial)*                                               |
| $\textsf{Rel}$  | Types                                  | *Relations*, i.e., programs `fn rel(a: A, b: B) ->  bool`                     |
| $\textsf{Perm}$ | Types                                  | *Invertible Rust programs*                                        |
| $\textsf{Fin}$  | Finite types                           | *Rust programs*                                                  |
| $\textsf{Mon}$  | Monoids                                | *Monoid homomorphisms*                                           |
| $\textsf{Rng}$  | Rings                                  | *Ring homomorphisms*                                             |
| $\textsf{Gpd}$  | Groups                                 | *Group homomorphisms*                                            |
| $\textsf{Top}$  | Topological spaces                     | *Continuous functions*                                           |
| $\textsf{Pnt}$  | Pointed types                          | *Programs preserving the point*                                   |
| $\textsf{Mat}$  | Natural numbers                        | *Matrices* $M_{n,m}$ with $n$ rows and $m$ columns                    |
| $\textsf{Cat}$  | Categories                             | *Functors*                                                       |
| $\textsf{Pre}$  | Preorders                              | *Monotone functions*                                             |
| $\textsf{Vect}$ | Vector spaces                          | *Linear functions*                                               |
| $\textsf{Hilb}$ | Complete inner product vector spaces   | *Bounded linear maps*                                            |
| $\textsf{CPO}$  | Complete partial orders                | *Monotone functions preserving least upper bounds*               |
| $[\mathbb C,\mathbb D]$       | Functors $\mathbb C \to \mathbb D$                   | *Natural transformations*                                        |
| $\textsf{PER}$ | Partial equivalence relations on ℕ     | *A function $\N \to \N$ with a natural number which realizes it as primitive recursive functions*        |

There are also some recipes which, given some data that you can pick, it constructs a new category for you.

- Given a graph, construct the free category.
The idea of "free" in this context is that *there are no other equations other than those necessary to satisfy the category laws.*

# Preorders

We saw that a category with one object is the same thing as a monoid.

What if the *one value* part was on the side of morphisms instead of objects?

## Definition: ???
- *(Objects).* A choice of a Rust type `Obj`
- *(Arrows).* For every value `a:Obj`, `b:Obj`, a Rust type *which MUST have either zero or one elements*
- *(Composition).* For every value `a:Obj`, `b:Obj`, `c:Obj`, a Rust function
```rust
fn compose[a][b][c](a: Arr[a,b], g: Arr[b,c]) -> Arr[a,c] {
    ...
}
```

# Categories where one picks a different notion of equality

Programs under syntactic equivalence, under semantic equivalence.

# Categories as languages

## Algebraic language for categories
## Graphical language for categories - Pasting diagrams
## Graphical language for categories - String diagrams
## Term language for categories
