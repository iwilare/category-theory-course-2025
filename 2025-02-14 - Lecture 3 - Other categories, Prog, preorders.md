# 2025-02-13 - Lecture 3 (Other categories)

## Today's plan

- We will see other examples of categories:
    - Prog, the category of programs
    - The free category on a graph

## Small remark on the notation for method chaining

We sometimes use method chaining instead of function application:

|  |  |  |
|-|-|-|
`f(g(x))` | = | `x.g().f()`
`f(g(x, b, c), k, m)` | = | `x.g(b, c).f(k, m)`

These are perfectly equivalent ways of expressing function application: the first is the usual one. The second is the order in which `compose` is defined in the definition of category.

(This cannot be done in Rust all the time like this! One would have to use `impl` to be able to use method calls.)

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


## Before finishing the definition: equality of programs

Important: We define equality in this category to be **program equivalence**: we say that two strings/symbols `f: Arr[A][B]` and `g: Arr[A][B]`, we consider these two strings to be the same when they are equivalent programs:

> **Program equivalence:** given two types `A` and `B` two programs `fn f(x: A) -> B` and `fn g(x: A) -> B`, we say that they are *equal* (written `f = g`) when: *for every value `x` of type `A`, the two calls `f(x)` and `g(x)` return the same value*.

For example, these two programs are not syntactically the same!

```rust
fn addv1(n: i32) -> i32 { 4 + (n + 3) }
fn addv2(n: i32) -> i32 { n + 7 }
```

However, they are clearly equivalent, because they always return the same value.

Similarly, these two programs are going to be defined very differently, and they are going to be compiled to different machine codes.

```rust
fn quicksort(l: List<i32>) -> List<i32> => ...
fn mergesort(l: List<i32>) -> List<i32> => ...
```
However, they are equivalent, since they return equal values for any list `l: List<i32>`.

**How do we establish program equivalence?**

In order to say when two functions are equal we introduce the notion of *symbolic reasoning*. This is an heuristic: it works to prove equality most of the times, but sometimes a stronger argument might be needed.

> *Symbolic reasoning* to establish `f = g`:
> 1. Assume to have a "symbolic" variable `x: A` with the type of the input.
> 2. Start with the expression `f(x)` and with the expression `g(x)`.
> 3. Repeat the following rules on any of the two expressions as long as necessary:
>    1. **(Function evaluation)** *Given a function `fn f(x: A) -> B => ...` and a value `v: A`, we can evaluate the expression `f(v)` by taking the body of the function and replacing the variable `x` with the value `v`. For example, the expression `addA(42)` can be unfolded to `4 + (42 + 3)`.*
>    2. **(General rule.)** Apply any equivalence which is valid because of general reasons. For example, given `a, b: Int`, we can replace `a + b` with `b + a` because addition is commutative.
>    3. **(Match evaluation.)** *(Not needed for now.)* ...
>    4. **(Case analysis.)** *(Not needed for now.)* ...
> 4. Repeat step 3. until the two expressions match *syntactically*.

### Example:

`addv1 = addv2`:
```rust
  addv1(x)      (Function evaluation.)
= 4 + (x + 3)   (General rule: commutativity of addition)
= 4 + (3 + x)   (General rule: associativity of addition)
= (4 + 3) + x   (General rule: evaluation of addition)
= 7 + x

  addv2(x)      (Function evaluation.)
= 7 + x
```

## Continuing the definition...

- *(Identities.)* We pick the arrow
    ```rust
    fn idA(a: A) -> A {
        a
    }
    ```
    So we picked a string, which is also a valid program.
- *(Composition.)*
    Given two programs `fn f(a: A) -> B`, `fn g(b: B) -> C`
    for the composition I give you the valid Rust program:
    ```rust
    fn compose_f_g(a: A) -> C {
        g(f(a))
    }
    ```
- (Identity left.) `compose(f, id[A]) = f`. These two clearly have the same behaviour:
    ```rust
    fn f(a: A) -> B {
        ...
    }
    =
    fn compose_f_g(a: A) -> B {
        id(f(a))
    }
    ```
    More formally: for any program `fn f(x: A) -> B`, we have that `compose_identityA_f = f`.
    ```
      compose_identityA_f(x)   (Function evaluation.)
    = x.identityA().f()        (Function evaluation.)
    = x.f()                    (General rule: method calls are just function calls.)
    = f(x)
    ```

- *(Identity right)*  For any program `fn f(x: A) -> B`, we have that `compose_id_identityB = f`.
    ```
      compose_f_identityB(x)   (Function evaluation.)
    = x.f().identityA()        (General rule: method calls are just function calls.)
    = identityA(x.f())         (Function evaluation.)
    = x.f()
    ```

- *(Associativity)*  For any program `fn f(x: A) -> B`, `fn g(x: B) -> C`, `fn h(x: C) -> D` we have that the two programs `compose_compose_f_g_h = compose_f_compose_g_h` are equivalent.

    The signature of the two morphisms is `fn f(x: A) -> D`, so it makes sense to ask whether they are equivalent or not.

    *Proof.*
    ```
      compose_compose_f_g_h(x)   (Function evaluation.)
    = x.compose_f_g().h()        (Function evaluation.)
    = (x.f().g()).h()            (General rule: convention selected for method chaining.)
    = x.f().g().h()

      compose_f_compose_g_h(x)   (Function evaluation.)
    = x.f().compose_g_h()        (Function evaluation.)
    = (x.f()).g().h()            (General rule: convention selected for method chaining.)
    = x.f().g().h()
    ```

# Categories where one picks a different notion of equality

The category of programs can be thought of in many different ways. Here are some possibilities:

1. *Syntactic equivalence* Two program symbols are equal when they are syntactically the same, i.e., they have the same source code.
2. *Temporal equivalence.* For every possible input value `a:A`, then the above function, called on a, `f(a)` and `compose_f_g(a)` take exactly the same amount of CPU cycles.
1. *Behavioural equivalence. (this is the default meaning of equality)* For every possible input value `a:A`, the two functions `f,g`, when called on a, give you two equal values of type `B`, i.e., one has that `f(a) = g(a)` between values of type `B`.

# The free category on a multi-edged graph

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
    This always holds!

### Important takeaway: in a preorder, every equation holds, i.e., any two elements of `Arr[a][b]` are equal, simply because there is at most one possible element in each `Arr[a][b]`.

## Example: the preorder of cities reachable by a flight

- *(Objects).* A type with names of cities.
- *(Arrows).* A type with one element when a city can be reached by a series of Finnair flights.
- *(Identities.)* Staying in the same city counts as a series of flights.
- *(Composition).*

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
