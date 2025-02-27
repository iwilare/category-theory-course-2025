# 2025-02-27 - Lecture 4 (Products)

## Today's plan

This lecture marks the end of introductory/preliminary material for category theory. From this lecture onwards, the real category theory begins, and we can start applying the power of categorical reasoning to show general properties of categories "for free".

- Introduce other important examples of categories (the preorder of natural numbers under two, the preorder of deductions),
- Define the notion of product.

# Example of category: the preorder $(\N,\le)$ of natural numbers using $\le$

- *(Objects.)* The type of natural numbers.
- *(Arrows.)* There is exactly one arrow from $n$ to $m$ when $n \le m$.
$$
0 \to 1 \to 2 \to \cdots
$$
- *(Identities.)* yes, because for every $n:\textsf{Nat}$, we have that $n \le n$.
- *(Composition.)* yes, because for every $n,m,k:\textsf{Nat}$, we have that, whenever $n \le m$ and $m \le k$, then $n \le k$.

# Example of category: the preorder $(\N,\textsf{divides})$ of natural numbers using $\textsf{divides}$

- *(Objects.)* The type of natural numbers.
- *(Arrows.)* There is exactly one arrow from $n$ to $m$ when $n$ divides $m$.

    For example: $3 \textsf{ divides } 9$, $2 \textsf{ divides } 12$, $10 \textsf{ divides } 10$, but $!(3 \textsf{ divides } 7)$, $!(4 \textsf{ divides } 2)$.
- *(Identities.)* yes, because for every $n:\textsf{Nat}$, we have that $n \textsf{ divides } n$.
- *(Composition.)* yes, because for every $n,m,k:\textsf{Nat}$, we have that, whenever $n \textsf{ divides } m$ and $m \textsf{ divides } k$, then $n \textsf{ divides } k$.

# Example of category: the preorder $(\text{BExpr}, \to)$ of boolean propositions and implications between them

Let's work with the type of boolean expressions, i.e., expressions that I can put as the condition of an if-then-else statements.

```rust
enum BExpr {
    LessEq(String, i32),
    GreaterEq(String, i32),
    And(BExpr, BExpr),
    Or(BExpr, BExpr),
    Not(BExpr),
    F,
    T,
}
```
Some examples:
- `x ≤ 7` (i.e., formally, `LessEq("x", 7)`).
- `!(y ≤ 3 && x ≥ 4)` (i.e., formally, `Not(And(LessEq("y", 3), GreaterEq("x", 4)))`).
- `!(x ≥ 0 || false)` (i.e., formally, `Not(Or(GreaterEq("x", 0), F))`).

We define the following category $\textsf{BExpr}$.

- *(Objects.)* The type `BExpr`.
- *(Arrows.)* There is exactly one arrow from `P` to `Q` whenever, knowing that `P` is true, I also know that `Q` is true.

    Examples:
    - `x ≤ 7` $\to$ `x ≤ 10`
    - `x ≤ 7 && x ≤ 4` $\to$ `x ≤ 5`
    - `!(x ≤ 7)` $\to$ `x ≥ 8`
    - `x ≤ 7 || false` $\to$ `x ≤ 7`

     In other words, there is an arrow whenever `P` implies `Q`.
     (In other words, there is an arrow whenever `!P || Q` is true.)

- *(Identities.)* yes, because if I know that `P` is true then `P` is also true.
- *(Composition.)* yes, because for every `P,Q,R:BExpr`:
    1. imagine I know that `P` implies `Q`.
    2. imagine I know that `Q` implies `R`.

    Now, assume I know that `P` is true. Then, by (1.), I know that `Q` is true. But then, by (2.) I know that `R` is true. Therefore, I managed to deduce that `R` is true from the fact that `P` is true.

# Definition: product of two objects $A$, $B$

Fix two objects $A,B$ in a category. These will stay fixed for the rest of the definition.

---

An object $P$ is said to be *a product of $A$ and $B$* if the following conditions are satisfied:

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A,$
    - $\textsf{snd} : P \to B,$
2. *(Existence of pairs.)* if someone gives you the following data,
    - another object $H$,
    - an arrow $f : H \to A,$ and
    - an arrow $g : H \to B,$

    then you must pick an arrow
    - $p : H \to P$,

    such that these equations hold:
    - $p \,; \textsf{fst} = f$,
    - $p \,; \textsf{snd} = g$.

    Since the choice of $p$ depends on the $f$ and $g$ that you gave me, we will instead write the arrow $p$ that *you* must pick as
    $$\langle f , g \rangle : H \to P.$$
    A less-mathy notation for this is just $\textsf{pairing}_{f,g} : H \to P.$

3. *(Pair expansion.)* the choice for $\langle \cdots , \cdots \rangle$ that you gave above must satisfy the following equation for every arrow $h : H \to P$:
$$
    \langle h \,; \textsf{fst} , h \,; \textsf{snd} \rangle = h.
$$

**End of definition.**

There are other equivalent formulations for products that you might find in the wild, they are all equivalent.

We will sometimes write $A \times B$ to indicate an object $P$ that satisfies the property of being a product of some other two objects $A,B$.

## Products sometimes might not exist:

In the category with two objects and only identities, the product of `A` and `B` does not exist (since there are no morphisms that connect `A` and `B`!)

```
   A           B
   .           .
```

## Examples of products: $\text{Prog}$

1. The category $\text{Prog}$ of Rust types and total programs between them.

Remember that composition of programs is given by the program
```rust
fn compose_f_g(a: A) -> C {
    g(f(a))
}

// Equivalently, we will use method chaining so that the two functions are in the same order as in the expression $f ; g$:

fn compose_f_g(a: A) -> C {
    a.f().g()
}
```

Moreover, remember that we will consider equivalence of programs as *behavioural equivalence*.

**For the rest of this lecture, we consider Rust with these restrictions:**

- `i32`, `String`, `bool` types
- Functions only take one argument, and as always never loop/crash, etc. and always terminate (this is what total means.)
- The `Pair<A,B>` type for any two types `A`, `B`, defined like this:
    ```rust
    struct Pair<A,B> {
        left: A,
        right: B,
    }
    ```
(In particular, these are also types: `Pair<i32,Pair<String,i32>>`, `Pair<Pair<String,String>,bool>`, etc...!)

## Let's show that $\text{Prog}$ has all products, where now `Prog` takes as objects all the types we just listed and all total programs between as arrows.

What does it mean to have "all products"? For any `A,B` types, I will give you a choice `P` of a type, and I'm going to tell you that this `P` that I chose really is a product.

*Proof.*

- *(Choice of $P$.)* For any two types `A`, `B`, we say that there is an object which satisfies the property of being a product of `A`, `B`. The object we select for $P$ is `Pair<A,B>`.
- *(Existence of projections.)* We need to provide two arrows $\texttt{fst}:\text{Prog}(\texttt{Pair<A,B>},\texttt A), \texttt{snd}:\text{Prog}(\texttt{Pair<A,B>},\texttt B)$. We define them like this:

    ```rust
    fn fst(p: Pair<A,B>) -> A {
        match p {
            Pair(left, right) => left
        }
        // p.left
    }

    fn snd(p: Pair<A,B>) -> B {
        match p {
            Pair(left, right) => right
        }
        // p.right
    }
    ```

- *(Existence of pairing.)* Take any type `H`, and assume we are given two programs

    ```rust
    fn l(v: H) -> A => ...
    fn r(v: H) -> B => ...
    ```

    The pairing  arrow `pairing_l_r` for `l` and `r` is defined as follows:

    ```rust
    fn pairing_l_r(v: H) -> Pair<A,B> {
        Pair(l(v), r(v))
        // equivalently: Pair { left: l(v), right: r(v) }
    }
    ```

    Let's verify the equations $\lang l,r \rang\,;\text{fst} = l$, $\lang l,r \rang\,;\text{snd} = r$.
    Remember that composition is "substitution" of one definition into the other, and that equality is program equivalence.

    - $\lang l,r \rang\,;\text{fst} = l$

        The first arrow is this program:
        ```rust
            fn compose_(pairing_l_r)_fst(v: H) -> A {
                v.pairing_l_r().fst()
            }
        ```
        Let's reason by program equivalence, for some argument `v: H`:
        ```rust
            v.pairing_l_r().fst()          // (Function expansion.)
            = Pair(l(v), r(v)).fst()       // (Function expansion.)
            = (match Pair(l(v),r(v)) {
                 Pair(a,b) => a
              })              // (Match evaluation.)
            = l(v)
        ```
        which is exactly the definition of ```fn l(v: H) -> A```.

    - $\lang l,r \rang\,;\text{snd} = r$

        The first arrow is this program:
        ```rust
            fn compose_(pairing_l_r)_snd(v: H) -> A =>
            v.pairing_l_r().snd()
        ```
        Let's reason by program equivalence, for some argument `v: H`:
        ```rust
            v.pairing_l_r().snd()          // (Function expansion.)
            = Pair(l(v), r(v)).snd()       // (Function expansion.)
            = (match Pair(l(v),r(v)) {
                Pair(a,b) => b
              })              // (Match evaluation.)
            = r(v)
        ```
        which is exactly the definition of ```fn r(v: H) -> A```.

- *(Pair expansion)*

    Assume to have a arrow `fn p(v: H) -> Pair<A,B>` which also satisfies the *product equalities*. We show that is program equivalent to `pairing_(compose_p_fst)_(compose_p_snd)`.

    Assume to have a value `v: H`.
    and let's assume that when we call `p` on this value we get a result `Pair(v1,v2)` for some values `v1: A` and `v2: B`.
    ```rust
           v.p()
         = Pair(v1, v2)        // (General principle.)
                               // Assumption that the result of this function is given
                               // by combining some values `v1`, `v2`.
    ```
    ```rust
           v.pairing_(compose_p_fst)_(compose_p_snd)  // (Function unfolding.)
         = Pair(v.compose_p_fst(), v.compose_p_snd()) // (Function unfolding.)
         = Pair(v.p().fst(), v.p().snd())             // (Assumption.)
         = Pair(Pair(v1,v2).fst(), Pair(v1,v2).snd()) // (Function unfolding.)
         = Pair(match Pair(v1,v2) {
                    Pair(a,b) => a
                },
                match Pair(v1,v2) {
                    Pair(a,b) => b
                })                     // (Match evaluation.)
         = Pair(v1, v2)
    ```

# Why are they called products?

Consider the category $\text{FinProg}$, which is just the ""sub-category"" (we have not defined this yet precisely) where the types are only the finite ones.

```rust
// Recall: general definition of the product, for any types `A`, `B`
struct Pair<A,B> {
    left: A,
    right: B
}
```

```rust
// Consider these two types:
enum Two {
    A1, A2
}

enum Three {
    B1, B2, B3
}

// Why are products called like this?
// How many possible ways are there to construct elements of this type?
struct Pair_Two_Three {
    left: Two,
    right: Three,
}

// Pair_Two_Three { left: A1, right: B1 }
// Pair_Two_Three { left: A1, right: B2 }
// Pair_Two_Three { left: A1, right: B3 }

// Pair_Two_Three { left: A2, right: B1 }
// Pair_Two_Three { left: A2, right: B2 }
// Pair_Two_Three { left: A2, right: B3 }
```

So, given two types `A,B`, such that
- $\textsf{size}(A) = n$, and
- $\textsf{size}(B) = m$, then
- $\textsf{size}(\texttt{Pair}(A,B)) = n * m$.

Now, the product of numbers (i.e., the size of types) is associative, commutative, etc... we will return back to this in the next lecture.

# Products in a category corresponds to pairs in a programming language.

Take the term language of some category $C$.

> If you tell me that $C$ has a product $A \times B$ for every object $A,B$, then the term language can assume to have a type `Pair<A,B>`, a pair constructor `Pair { left: ..., right: ... } `,  and `match` constructs.

```rust
struct Pair<A,B> {
    left: A,
    right: B
}
```

These choices are "well-behaved" because of the properties of products.

- > *Being able to construct elements of a pair is the same as the existence of the pairing arrow.*

    Informally:

    ```rust
    Pair(<expr1>[x], <expr2>[x])
    ```

    can be replaced by

    ```rust
      pairing_expr1_expr2[x]
    = Pair(<expr1>[x], <expr2>[x]) // (Our definition of `pairing_l_r`)
    ```
    (we need to consider `expr1` and `expr2` as "functions" which take their free variables as arguments.)

- > *Being able to `match` is the same as having `fst` and `snd` as functions of our language.*

    ```rust
    match <expr> {
        Pair(a,b) => <body>[a,b]
    }
    ```
    becomes
    ```rust
    <body>[<expr>.fst(),<expr>.snd()]
    ```

    For example:

    ```rust
    fn isAdultWithNameLength(p: Pair<i32, String>) -> Pair<bool, i32> {
        match p {
            Pair(age,name) => Pair(age >= 18, name.length())
        }
    }
    ```

    is program equivalent to

    ```rust
    fn isAdultWithNameLength(p: Pair<i32, String>) -> Pair<bool, i32> {
        Pair(p.fst() >= 18, p.snd().length())
    }
    ```
- > The product equalities of the pairing arrow say how the `fst` and `snd` functions behave, and allows us to use the *(Match evaluation)* rule when running a program and in our program equalities. (Remember that `fst` and `snd` were defined by match evaluation.)
- > The pair expansion property for the pairing arrow says that pairs are determined by their `fst` and `snd`, and nothing else.

    ```rust
    let (a,b) = p
    return Pair(a,b)
    ```

    ```rust
    match p {
        Pair(a,b) => Pair(a,b)
    }
    ```

    really is the same as just avoiding decomposition and recomposition

    ```rust
    p
    ```

# Examples of products: $(\N, \le)$

Idea: a "product" of the two numbers $A$ and $B$ always exists, and it's the number $\min\{A,B\}$.

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A, \qquad \min\{A,B\} \le A,$
    - $\textsf{snd} : P \to B, \qquad \min\{A,B\} \le B,$
2. *(Existence of pairs.)* if someone gives you the following data,
    - another object (number) $H$,
    - two arrows $f : H \to A, \quad$ i.e., I know that $H \le A,$
    - two arrows $g : H \to B, \quad$ i.e., I know that $H \le B,$

    then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that $H \le \min\{A,B\}.$

    ~~such that these equations hold:~~ (this does not need to be checked since we are in a preorder!)
    - ...

**In other words, $\min\{A,B\}$ is the "largest" number that is smaller than both $A$ and $B$.**

*A choice that unfortunately is not a product:* given two numbers $a$, $b$, I pick as their product the number $1$. Projections exist but the existence of pairs condition fails.

# Examples of products: $(\N, \textsf{divides})$

Idea: a "product" of two numbers $A$ and $B$ always exists, and it's the number $\gcd\{A,B\}$.

Example: $A = 28, B = 12$, which are just $7*4$ and $3*4$.

Then, $\gcd\{A,B\} = 4$. (Note! $2$ also divides both of them, but it's not the smallest divisor.)

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A, \qquad \gcd\{A,B\} \textsf{ divides } A,$
    - $\textsf{snd} : P \to B, \qquad \gcd\{A,B\} \textsf{ divides } B,$
2. *(Existence of pairs.)* if someone gives you the following data,
    - another object (number) $H$,
    - two arrows $f : H \to A, \quad$ i.e., I know that $H \textsf{ divides } A,$
    - two arrows $g : H \to B, \quad$ i.e., I know that $H \textsf{ divides } B,$

    then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that $H \textsf{ divides } \gcd\{A,B\}.$

    ~~such that these equations hold:~~ (this does not need to be checked since we are in a preorder!)
    - ...

**In other words, $\gcd\{A,B\}$ is the "largest" divisor that divides both $A$ and $B$.**

# Examples of products: $(\text{BExpr}, \to)$

Idea: a "product" of two propositions `A` and `B` always exists, and it's the proposition `A && B`.

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A, \qquad$ the implication `A && B` $\to$ `A`
    - $\textsf{snd} : P \to B, \qquad$ the implication `A && B` $\to$ `B`

    Intuitively, this works! If `A && B` is true then certainly `A` is true.
2. *(Existence of pairs.)* if someone gives you the following data,
    - another object (proposition) `H`,
    - two arrows $f : H \to A, \qquad$ the implication `H` $\to$ `A`,
    - two arrows $g : H \to B, \qquad$ the implication `H` $\to$ `B`,

    then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that `H` $\to$ `A && B`.

    ~~such that these equations hold:~~ (this does not need to be checked since we are in a preorder!)
    - ...

---

## TODO: correctness, no garbage

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A, \qquad$ the implication `A && B && true` $\to$ `A`
    - $\textsf{snd} : P \to B, \qquad$ the implication `A && B && true` $\to$ `B`

    Intuitively, this works! If `A && B && true` is true then certainly `A` is true.
2. *(Existence of pairs.)* if someone gives you the following data,
    - another object (proposition) `H`,
    - two arrows $f : H \to A, \qquad$ the implication `H` $\to$ `A`,
    - two arrows $g : H \to B, \qquad$ the implication `H` $\to$ `B`,

    then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that `H` $\to$ `A && B && true`.

    ~~such that these equations hold:~~ (this does not need to be checked since we are in a preorder!)
    - ...

---

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A, \qquad$ the implication `A && B && x == 0` $\to$ `A`
    - $\textsf{snd} : P \to B, \qquad$ the implication `A && B && x == 0` $\to$ `B`

    Intuitively, this works! If `A && B && x == 0` is true then certainly `A` is true.
2. *(Existence of pairs.)* if someone gives you the following data,
    - another object (proposition) `H`,
    - two arrows $f : H \to A, \qquad$ the implication `H` $\to$ `A`,
    - two arrows $g : H \to B, \qquad$ the implication `H` $\to$ `B`,

    then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that `H` $\to$ `A && B && x == 0`.

    ~~such that these equations hold:~~ (this does not need to be checked since we are in a preorder!)
    - ...
































# Definition: isomorphisms

Two objects $A$ and $B$ are said to be *isomorphic*, which we write as $A \cong B$, whenever the following thing happens:

You provide two arrows
- $\textsf{a2b} : A \to B,$
- $\textsf{b2a} : B \to A,$

such that the following two equations hold:

$$\textsf{a2b} \,; \textsf{b2a} = \textsf{id}_A, \quad \textsf{b2a} \,; \textsf{a2b} = \textsf{id}_B.$$

## Theorem: being isomorphic is a reflexive notion.

For any object $A$, then $A \cong A$.

## Theorem: being isomorphic is a symmetric notion.

For any object $A,B$, if I know that $A \cong B$, then $B \cong A$.

## Theorem: being isomorphic is a transitive notion.

For any object $A,B,C$, if I know that $A \cong B$ and that $B \cong C$, then I know that $A \cong C$.

# The first important idea in category theory:
## Theorem ("Being a product" is a pairing property) -> Take $A,B$ objects. Any two products $P$ and $Q$ of the same $A$, $B$ are isomorphic.
