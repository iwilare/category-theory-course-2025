# 2025-03-07 - Lecture 7 (Universal properties, opposite, coproducts)

## Today's plan
- Talk about universal properties (for products)
- Talk about opposite categories
- Talk about coproducts

> ## (\*) The second important idea in category theory: universal properties single out specific constructions among all the other ones, in a unique way; for instance, they allow you to talk not about being "a product" but being "the product" without essentially losing any information.

## Theorem ("Being a product" is a universal property)

Take $A,B$ objects. Any two products $P$ and $Q$ of the same $A$, $B$ are isomorphic.

*Proof*.

Assume that we are given $P$ and $Q$, such that we know that both of them are products.

*Task objective.* Prove that $P \cong Q$.

*Task objective.* Build two maps $\textsf{p2q} : P \to Q, \textsf{q2p} : Q \to P$, show that $$\textsf{p2q} \,; \textsf{q2p} = \textsf{id}_P, \quad \textsf{q2p} \,; \textsf{p2q} = \textsf{id}_Q.$$

Solution:

Remember that
- $\textsf{fst}_{P} : P \to A$
- $\textsf{snd}_{P} : P \to Q$
- $\textsf{fst}_{Q} : Q \to A$
- $\textsf{snd}_{Q} : Q \to Q$
- $\langle l, r \rangle_P : H \to P$ for any $l : H \to A, r : H \to B$
- $\langle l, r \rangle_Q : H \to Q$ for any $l : H \to A, r : H \to B$

Then, the conversion arrows are the following ones:

$$
\begin{array}{ll}
    \textsf{p2q} := \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q : P \to Q \\
    \textsf{q2p} := \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P : Q \to P \\
\end{array}
$$

We show that $\langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q \,;  \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P = \textsf{id}_{P}$.

$$
\begin{array}{lll}
  & \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q \,;  \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P & \text{(naturality of pairing for $P$)} \\
= & \langle \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q \,; \textsf{fst}_{Q}, \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q \,; \textsf{snd}_{Q} \rangle_P & \text{(pairing equations for $Q$)} \\
= & \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_P & \text{(pair expansion for $P$)} \\
= & \textsf{id}_P & \text{(pair expansion for $P$)}
\end{array}
$$

We show that $\langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P \,;  \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q = \textsf{id}_{Q}$.

$$
\begin{array}{lll}
  & \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P \,;  \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q & \text{(naturality of pairing for $Q$)} \\
= & \langle \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P \,; \textsf{fst}_{P}, \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P \,; \textsf{snd}_{P} \rangle_Q & \text{(pairing equations for $P$)} \\
= & \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_Q & \text{(pair expansion for $Q$)} \\
= & \textsf{id}_P & \text{(pair expansion for $Q$)}
\end{array}
$$

# Opposite category

Here is a recipe that given a category gives you another, different, category for free.

Given any category $C$, I construct the following category, which I call $C^\textsf{op}$:
- *(Objects)*: the same objects of $C$,
- *(Arrows)*: I need to choose type `Arr[a][b]` for every `a,b:Obj`. I will chose each arrow type of my new category to be `Arr[b][a]` (note the swap!).

In order to distinguish the choice of arrows, we write `Arr[a][b]` and `Arrop[a][b]`.
- *(Identities)*: Since `Arrop[a][a] = Arr[a][a]`, I can simply *reuse* the original `id[a]: Arr[a][a]`.
- *(Composition)*: I need to give a Rust function
```rust
type Arrop[a][b] = Arr[a][b]

fn compose[a][b][c](f: Arrop[a,b], g: Arrop[b,c]) -> Arrop[a,c] {
    ...
}
fn compose[a][b][c](f: Arr[b,a], g: Arr[c,b]) -> Arr[c,a] {
    compose[c][b][a](g, f)
}
```
- *(Left unitality, right unitality, associativity)*. These all follow from the fact that in my choice of compose I simply used the original compose, which already satisfied these properties.

### Example of opposite category

Say that the category $C$ is defined like this, with four objects and some arrows:

```
   A ----> B -----> D
           |
           |
           |
           V
           C
```

Then this $C^{\textsf{op}}$ is also a category:

```
   A <---- B <----- D
           ^
           |
           |
           |
           C
```

### Example: $\text{Prog}^{\textsf{op}}$

- *(Objects.*) Types in Rust.
- *(Arrows.*) The type of arrows from `A` to `B` is defined to have a value whenever there is a (well-typed) Rust program from `B` to `A`.

(Again, unlike the very first examples of category we cannot define this category *within* Rust because it's not expressive enough. For other languages this can be done.)


# Definition: Coproduct of two objects $A$, $B$

Given two objects $A,B$, an object $P$ is called *a coproduct* whenever $P$ is a product in the category $C^{\textsf{op}}$, where all I have done is precisely swap the arrows. (this makes sense only because $C$ and $C^{\textsf{op}}$ have exactly the same objects)

For comparison with the definition of product I will put on the right in gray the original definition of products.

What does the definition above explicitly say? It says that given two objects $A,B$, an object $P$ is called *a coproduct* whenever the following conditions are satisfied:

1. *(Existence of coprojections, also called injections.)* you must pick two arrows
    - $\textsf{inl} : A \to P,$ $\color{grey}{\qquad\textsf{fst} : P \to A,}$
    - $\textsf{inr} : B \to P,$ $\color{grey}{\qquad\textsf{snd} : P \to B,}$
2. *(Existence of copairs, also called "case analysis".)* if someone gives you the following data,
    - another object $H$,
    - an arrow $l : A \to H,$  $\color{grey}{\qquad l : H \to A,}$
    - an arrow $r : B \to H,$  $\color{grey}{\qquad r : H \to B,}$

    then you must pick an arrow
    - $p : P \to H$,              $\color{grey}{\quad \qquad p : H \to P,}$

    such that these equations hold:
    - $\textsf{inl} \,; p = l$, $\color{grey}{\qquad p \,; \textsf{fst} = l}$
    - $\textsf{inr} \,; p = r$. $\color{grey}{\qquad p \,; \textsf{snd} = r}$

    Since the choice of $p$ depends on the $f$ and $g$ that you gave me, we will instead write the arrow $p$ that *you* must pick as
    $$[ l , r ] : H \to P.$$
    We will sometimes also denote it as $\textsf{cases}_{A,B} : H \to P$.
    With this notation, the equations above become the following:

    - $\textsf{inl} \,; [l,r] = l$, $\color{grey}{\qquad \langle l , r \rangle \,; \textsf{fst} = l}$
    - $\textsf{inr} \,; [l,r] = r$. $\color{grey}{\qquad \langle l , r \rangle \,; \textsf{snd} = r}$

3. *(Copair/cases expansion.)* the choice for $[ \cdots , \cdots ]$ that you gave above must satisfy the following equation for every object $H$ and every arrow $h : H \to P$:
$$
    [ \textsf{fst} \,; h  , \textsf{snd} \,; h] = h. \\
    \color{grey}{\langle h \,; \textsf{fst}  , h \,; \textsf{snd} \rangle = h.}
$$

**(End of definition.)**

# Examples of coproducts: in $\text{Prog}$

- *(Choice of $P$.)* For any two types `A`, `B`, we say that there is an object which satisfies the property of being a product of `A`, `B`. The object we select for $P$ is `Either<A,B>`, defined as follows:

    ```rust
    enum Either<A,B> {
        Left  { value: A },
        Right { value: B },
    }

    enum Either<A,B> {
        Left(A),
        Right(B),
    }

    // Left(3)        : Either<i32, String>
    // Left(3)        : Either<i32, float>
    // Right("hello") : Either<i32, String>
    // Right(42)      : Either<i32, i32>
    ```

- *(Existence of coprojections/injections.)* We need to provide two arrows $\texttt{inj}: \texttt A \to \texttt{Either<A,B>}, \texttt{inr}: \texttt B \to \texttt{Either<A,B>}$, i.e., construct certain Rust programs with the given signature. We define them like this:

    ```rust
    fn inl(v: A) -> Either<A, B> {
        Left(v)
    }
    fn inl(v: B) -> Either<A, B> {
        Right(v)
    }
    ```
    i.e., `inl = Left` and `inl = Right` are essentially the same maps.

    Notice the reversal of arrows with respect to the definition of products:
    ```rust
    fn fst(p: Pair<A, B>) -> A {
        p.first
    }
    fn snd(p: Pair<A, B>) -> B {
        p.second
    }
    ```

- *(Existence of copairing/case analysis.)* Assume someone comes up to us and gives us a type `H` and two programs

    ```rust
    fn l(v: A) -> H { ... }
    fn r(v: B) -> H { ... }
    ```

    The pairing arrow `cases_l_r` for `l` and `r` is defined as follows:

    ```rust
    fn cases_l_r(p: Either<A,B>) -> H {
        match p {
            Left(a) => l(a),
            Right(b) => r(b),
        }
    }
    ```

    Let's verify the equations
    - $\textsf{inl} \,; [l, r] = l$,
    - $\textsf{inr} \,; [l, r] = r$.

    Remember that composition is "substitution" of one definition into the other, and that equality is program equivalence.

    - $\textsf{inl} \,; [l, r] = l$

        The first arrow is this program:
        ```rust
        fn compose_inl_(cases_l_r)(v: A) -> H {
            v.inl().cases_l_r()
        }
        ```
        Let's reason by program equivalence, for some argument `v: A`:
        ```rust
              v.inl().cases_l_r()   // (Function expansion.)
            = Left(v).cases_l_r()   // (Function expansion.)
            = (match Left(v) {
                  Left(a) => l(a),
                  Right(b) => r(b),
              })                    // (Match evaluation.)
            = l(v)
        ```
        which is exactly the definition of ```fn l(v: A) -> H```.

    - $\textsf{inr} \,; [l, r] = r$

        The first arrow is this program:
        ```rust
        fn compose_inr_(cases_l_r)(v: B) -> H {
            v.inr().cases_l_r()
        }
        ```
        Let's reason by program equivalence, for some argument `v: B`:
        ```rust
              v.inr().cases_l_r()   // (Function expansion.)
            = Right(v).cases_l_r()  // (Function expansion.)
            = (match Right(v) {
                  Left(a) => l(a),
                  Right(b) => r(b),
              })                    // (Match evaluation.)
            = r(v)
        ```
        which is exactly the definition of ```fn r(v: H) -> A```.

- *(Copair expansion)*

    Assume to have a morphism `fn s(v: Either<A,B>): X`. We show that is program equivalent to `cases_(compose_left_s)_(compose_right_s)`.

    Assume to have a value `v: Either<A,B>`.
    There are two cases to check:
    - **either** `v = Left(a)` for some value `a`,
    - or `v = Right(b)` for some value `b`.

    In both cases, we verify that the conclusion holds, and these are only two cases we need to check.

    1. case `v = Left(a)` for some value `a: A`:
    ```rust
        v.cases_(compose_inl_s)_(compose_inr_s)       // (Assumption.)
      = Left(a).cases_(compose_inl_s)_(compose_inr_s) // (Function unfolding.)
      = match Left(a) with
        | Left(a) => compose_inl_s(a)
        | Right(b) => compose_inr_s(b)                  // (Match evaluation.)
      = compose_inl_s(a)                              // (Function unfolding.)
      = a.inl().s()                                   // (Function unfolding.)
      = Left(a).s()                                   // (Assumption.)
      = v.s()
    ```
    2. case `v = Right(b)` for some value `b: B`:
    ```rust
        v.cases_(compose_inl_s)_(compose_inr_s)        // (Assumption.)
      = Right(b).cases_(compose_inl_s)_(compose_inr_s) // (Function unfolding.)
      = match Right(b) with
        | Left(a) => compose_inl_s(a)
        | Right(b) => compose_inr_s(b)                 // (Match evaluation.)
      = compose_inr_s(b)                               // (Function unfolding.)
      = b.inr().s()                                    // (Function unfolding.)
      = Right(b).s()                                   // (Assumption.)
      = v.s()
    ```


# Duality

Facts about duality:

- Coproducts in $C$ are just products in $C^\textsf{op}$.
- Products in $C$ are just coproducts in $C^\textsf{op}$.

| Map | Interpretation in Rust/the term language |
|-|-|
| $\textsf{fst} : A \times B \to A$ | used for `match`ing |
| $\textsf{snd} : A \times B \to B$ | used for `match`ing |
| $\textsf{inl} : A \to A + B$ | used as constructors |
| $\textsf{inr} : B \to A + B$ | used as constructors |
|-|-|
| $\langle -,- \rangle : H \to A \times B$ | used as constructor |
| $[-,-] : A + B \to H$ | used for `match`ing |

Asking for the existence of (co)products is the same as asking for the existence of these Rust programs:

```rust
fn pairing[a][b][h](f: Arr[h, a], g: Arr[h, b]): Arr[h, axb] {
    ...
}

fn cases[a][b][h](f: Arr[a, h], g: Arr[b, h]): Arr[a+b, h] {
    ...
}
```

# EXERCISES

Show that the following theorems are true: whenever you find an "if", you're allowed to imagine that someone has given to you some data/arrows/objects, and you're allowed to give a name to such objects/arrows that you imagine to have. For instance, to show that "if $A \cong B$..." then you can assume that someone has given you two arrows $f : A \to B$ and $g : B \to A$ (the names are arbitrary) and they have also told you that some equation holds (and you can use these in your proof.)

## Exercise 7.1

Convince yourself that the category $(C^{\textsf{op}})^{\textsf{op}}$ is the "same" as the category $C$, i.e.:
- The objects of $(C^{\textsf{op}})^{\textsf{op}}$ are the same type,
- For every $A,B$ the types of arrows from $A$ to $B$ of these categories are the same type.

## Exercise 7.2

Show that:
- if $A \cong B$ in the category $C$,
- then $A \cong B$ are also isomorphic in the category $C^{\textsf{op}}$
- (remember! This last statement makes sense because $A,B$ can be thought of as objects of $C$ or as objects of $C^\textsf{op}$.)

## Exercise 7.3

Show that:
- if $P$ is a product (for some $A$ and $B$),
- and $P \cong P'$ for some other $P'$,
- then $P'$ is also a product (of $A$ and $B$)

## Exercise 7.5

Show that:
- if $P$ is a product of $A$ and $B$,
- and if $A \cong A'$ for some other object $A'$,
- and if $B \cong B'$ for some other object $B'$,
- then $P$ also satisfies the property of being a product of $A'$ and $B'$.

## Exercise 7.6

Show that in every generic category $C$ that has products and coproducts you can construct an arrow with the following signature:

$$
\textsf{dist} : (A \times B) + (A \times C) \longrightarrow A \times (B + C)
$$
