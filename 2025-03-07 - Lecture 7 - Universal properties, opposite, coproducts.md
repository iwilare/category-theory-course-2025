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
    Notice the reversal of arrows:
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
    fn r(v: A) -> H { ... }
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
        fn comp_inl_(cases_l_r)(v: A) -> X {
            v.inl().cases_l_r()
        }
        ```
        Let's reason by program equivalence, for some argument `a: A`:
        ```rust
            v.inl().cases_l_r()          // (Function expansion.)
            = Pair(l(v), r(v)).fst()       // (Function expansion.)
            = (match Pair(l(v),r(v)) {
                 Pair(a,b) => a
              })              // (Match evaluation.)
            = l(v)
        ```
        which is exactly the definition of ```fn l(v: H) -> A```.

    - $\lang l,r \rang\,;\textsf{snd} = r$

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

> RECALL:
>
>   *(Pair expansion.)* the choice for $\langle \cdots , \cdots \rangle$ that you gave above must satisfy the equation $\langle h \,; \textsf{fst} , h \,; \textsf{snd} \rangle = h$ holds for every arrow $h : H \to P$.

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



# Duality




# EXERCISES

## Exercise 7.1

Convince yourself that the category $(C^{\textsf{op}})^{\textsf{op}}$ is the "same" as the category $C$, i.e.:
- The objects of $(C^{\textsf{op}})^{\textsf{op}}$ are the same type,
- For every $A,B$ the types of arrows from $A$ to $B$ of these categories are the same type.

## Exercise 7.2

Show that if $A \iso B$ in the category $C$ then $A \iso B$ are also isomorphic in the category $C$
