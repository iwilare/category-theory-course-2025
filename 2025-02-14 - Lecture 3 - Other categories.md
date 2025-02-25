# 2025-02-13 - Lecture 3 (Other categories)

## Today's plan

- We will see other examples of categories:
    - Prog, the category of programs
    - The free category on a graph

## The category $\textsf{Prog}$

- *(Objects.)* One object symbol for each possible type you can write in Rust. (this is essentialy `str` but only considering those types that are valid Rust types)

```rust
enum Obj {
    A, // a symbol for a certain Rust program,
    B, // a symbol for a another Rust program,
    ...
}
```

- *(Arrows.)* One arrow symbol from `A` to `B` for each possible program, with a single argument, that can be written in Rust. Same thing here for programs, only strings that are valid Rust programs.
- *(Identities.)* We pick the arrow
```rust
fn idA(a: A) -> A {
    a
}
```
So we picked a string, which is also a valid program.
- *(Composition.)*
    Given two programs `fn f(a: A) -> B`, `fn g(b: B) -> C`
    for the composition I give you
    ```rust
    fn composefg(a: A) -> C {
        g(f(a))
    }
    ```
- (Right identity.) `compose(f, id[A]) = f`.
    ```rust
    fn f(a: A) -> B {
        ...
    }
    =
    fn composefg(a: A) -> B {
        id(f(a))
    }
    ```

1. *Behaviour. (default meaning of equality)* For every possible input value `a:A`, then the above function, called on a, `f(a) = composefg(a)` between values of type B.
2. *Runtime behaviour.* For every possible input value `a:A`, then the above function, called on a, `f(a)` and `composefg(a)` take exactly the same amount of CPU cycles.

## The free category on a multi-edged graph

There are also some recipes which, given some data that you can pick, it constructs a new category for you.

- Idea behind free categories: *Given a graph, construct the free category.*

construct this category:
- *(Objects.)* Exactly the nodes of the graph.
- *(Arrows.)* Lists of edges from the graph that get me from point A to point B.
- *(Identities.)*
- *(Composition.)* Concatenation of paths, i.e., list concatenations

The idea of "free" in this context is that *there are no other equations other than those necessary to satisfy the category laws.*

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
| $\textsf{Prob}$  | Types                              | *Probability distributions*  `fn rel(a: A, b: B) ->  f32`   |

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

What if the *one value* part was on the side of arrows instead of objects?

## Definition: preorder

Idea: two types `A`, `B` are connected when the type `Arr[a,b]` has one element. They are not connected when the type is empty. We do not care *how* they are connected (in categories, we care about which program we run! Not all program symbols from one type to the other are the same.)

1. *(Objects).* A choice of a Rust type `Obj`;
2. *(Arrows).* For every value `a:Obj`, `b:Obj`, a Rust type *which MUST have either zero or one elements*;
```rust
// These are the only two choices
enum Arr_A_B {

}
enum One {
    One
}
enum Hello {
    World
}
```

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


                       // 1 el.     // 1 el.     // 0 el.
fn compose[a][b][c](a: Arr[a,b], g: Arr[b,c]) -> Arr[a,c] {

}
                     // -        // -            // 1 el.
fn compose[a][b][c](a: Arr[a,b], g: Arr[b,c]) -> Arr[a,c] {
    One
}
                    // 0 el.     // -              // 0 el.
fn compose[a][b][c](a: Arr[a,b], g: Arr[b,c]) -> Arr[a,c] {
    match a {

    }
}


// The idea is that asking that a composition map exists is the same as saying that the following condition holds:
//
//     *If both Arr[a,b] and Arr[b,c] have an element, then Arr[a,c] must have an element too.*
```
Subject to the following laws:

4. *(Left identity).* Given a value `f: Arr[a,b]` for some `a:Obj,b:Obj`,
    ```rust
    compose[a][b][b](f, id[b]) = f
    ```
    This always holds!
5. *(Right unitality.)* Given a value `f: Arr[a,b]` for some `a:Obj,b:Obj`,
    ```rust
    compose[a][a][b](id[a], f) = f
    ```
    This always holds!
7. *(Associativity.)* For every value `a,b,c,d: Obj`, and every value `f:Arr[a,b], g:Arr[b,c], h:Arr[c,d]`


## Example: the preorder of cities reachable by a flight

- *(Objects).* A type with names of cities.
- *(Arrows).* A type with one element when a city can be reached by a series of Finnair flights.
- *(Identities.)* Staying in the same city counts as a series of flights.
- *(Composition).*

# Categories where one picks a different notion of equality

The category of programs can be thought of in many different ways.

- Programs under syntactic equivalence: two program symbols are equal when they have the same source code.
- Programs under temporal equivalence: two program symbols are equal when they have the
- Programs under behavioural equivalence: two programs symbols are ...

### Other examples of categories:

- Trips for the free graph, trips under the equality "have the same price".
- Trips for the free graph, trips under the equality "take the same length".

The free category has a special property: it identifies the least amount of things together.
We will talk about this again when we talk about adjunctions.

# Other categories: the generic span, the interval, the square, the unit category

- The interval:
```
    f
A ----> B
```
- The generic span:
```
    f       g
A <---- B ----> C
```
- The "non-commutative" square:
```
      f
  A ----> B
  |       |
h |       | g
  |       |
  v       v
  C ----> D
      i
```

```rust
enum Arr_A_D {
    FG,
    HI,
}
fn composeABD(a: Arr_A_B, b: Arr_B_D) -> Arr_A_D {
    FG
}
fn composeACD(a: Arr_A_C, b: Arr_C_D) -> Arr_A_D {
    HI
}

```

- The "commutative" square:
```
      f
  A ----> B
  |       |
h |       | g
  |       |
  v       v
  C ----> D
      i
```
such that $f \,; g = h \,; i$.



```rust
enum Arr_A_D {
    K,
}
fn composeABD(a: Arr_A_B, b: Arr_B_D) -> Arr_A_D {
    K
}
fn composeACD(a: Arr_A_C, b: Arr_C_D) -> Arr_A_D {
    K
}

```

Commutative comes from vertical and then horizontal or horizontal and then vertical (think of commutativiy of addition: $a+b = b+a$)
