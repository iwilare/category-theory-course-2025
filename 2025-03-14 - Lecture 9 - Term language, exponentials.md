# 2025-03-14 - Lecture 8 (Term language, exponential)

## Today's plan

- Introduce the definition of exponential object
- See examples of exponential objects in two of the categories we have seen

# Digression on term language:

At the beginning we saw that we restricted our Rust programs to only programs that take a single argument (and that have no effects). We will not lift this restriction to effects yet, but we will expans a little bit our language possibilities: the idea is that certain categorical features allow us to equivalently "translate"/compile these extra features in terms of such structure, and therefore allow us to go back to the original restrictions where, e.g., each Rust program must still take a single argument.


1. *Claim.* If $C$ is a category with *a product for every $A$,$B$*, then the term language is allowed to:
    - have a type `Pair<A,B>` for every type `A`,`B`,
    - use projections (i.e., `a.first`, `a.second`),
    - use pair constructors `Pair { first: a, second: b }`,
    - use pattern matching on pairs. (`match e { Pair(a, b) => ... }`)
    - **In particular**, this allows for programs in the term language to take *more than one argument*.
    *Translation idea:*
    ```rust
    // A function with two arguments
    fn program(a: A, b: B) {
        ... a ... b ...
    }
    // equivalently...
    fn program(p: Pair<A,B>) {
        ... p.first ... p.second ...
    }

    // A function with three arguments
    fn program(a: A, b: B, c: C) {
        ...
    }
    // equivalently:
    fn program(a: Pair<A,Pair<B,C>>) {
        ...
    }
    fn program(a: Pair<Pair<B,C>,A>) {
        ...
    }
    ```
2. *Claim.* If $C$ is a category with a *terminal object* $A$, called "`Unit`", then the term language is allowed to:
    - have a type `Unit`,
    - use the constructor `Unit { }`,
    - use pattern matching on terminals. (`match e { Unit { } => ... }`)
    - **In particular**, this allows for programs in the term language to take *no arguments*.
    - **In particular**, this allows for programs in the term language to discard/ignore/throw away values.
    *Translation idea:*
    ```rust
    // A function with no arguments
    fn program() {
        ...
    }
    // equivalently:
    fn program(a: Unit) {
        ...
    }
    // equivalently:
    fn program(a: A) {
        ...
    }
    fn program(b: A) {
        ...
    }
    // ...but then `b` is ignored.
    ```
    *Important point* Ignoring values can only be done because of the existence of terminal objects.
    If you look at the term language for general categories that was introduced in Lecture 4, the argument cannot be discarded!
3. *Claim.* If $C$ is a category with an *initial object* $A$, called "`Empty`", then the term language is allowed to:
    - have a type `Empty`,
    - use the "destructor"/pattern matching `match e { }` (no clauses!)
    - **In particular**, this allows for programs to "not return a value"/return *into any other value*.
    *Translation idea:*
    ```rust
    // A function which never returns
    fn program(a: A) -> B {
        ...
        let b: Empty = f(...)
        match e {

        }
    }
    fn program(a: A) -> C {
        ...
        let b: Empty = f(...)
        match e {

        }
    }
    ```
    (Side remark for the curious: having *some* effects is consistent with having an initial object.)
4. *Non-claim.* If $C$ is a category with all coproducts, **IT IS NOT TRUE** that the term language is allowed to:
    - have a type `Either<A,B>`,
    - use the two constructors `Left(a)` and `Right(b)` for some values `a:A`, `b:B`,
    - use pattern matching on values `Either`
        ```rust
        match e {
            Left(a) => ...
            Right(b) => ...
        }
        ```
    - **IT IS TRUE IF AND ONLY IF** the following restriction applies: each branch in the left/right case is only allowed to *use* the values from the pattern matching BUT NOT other values which appear elsewhere in the context.

*We will now explain why this last example.*

> RECALL the following exercise:
> ### Exercise 7.6
> Show that in every generic category $C$ that has products and coproducts you can construct an arrow with the following signature:
> $$
> \textsf{dist} : (A \times B) + (A \times C) \longrightarrow A \times (B + C)
> $$

> Question: is this arrow part of an isomorphism $(A \times B) + (A \times C) \cong A \times (B + C)$?
>
> Answer: not necessarily. The arrow
> $$
> \textsf{opdist} :  A \times (B + C)  \longrightarrow (A \times B) + (A \times C)
> $$
> might not always exist, e.g.,
>
> *Claim*: in $\text{Pointed}$ an arrow like this such that $\textsf{opdist} \,; \textsf{dist} = \textsf{id}$ and $\textsf{dist} \,; \textsf{opdist} = \textsf{id}$ does not exist.

#### **Intuition steps for why `match` on `Either` in the term language is NOT modeled just by the existence of coproducts**:

1. The inverse of the distributivity map might not exist in every category... (you will have to trust me on this, for now.)
 intuitively, because:
    - a product $A \times B$ allows you to construct new arrows *into* it,
    - a coproduct $A + B$ allows you to define new maps *out* of it.

    But there is nothing in principle that tells me how to construct a map *into* a coproduct or *out* of a product!
2. Unfortunately, we *can* write such an inverse in an internal language (i.e., similar to a program in $\texttt{Prog}$, but remember, this is a new made up language for which we're trying to reconstruct the definition of):
    ```rust
    fn opdist(p: Pair<A, Either<B, C>>) -> Either<Pair<A, B>, Pair<A, C>> {
        match p.second {
            Left(b) => Left(Pair(p.first, b)),
            Right(c) => Right(Pair(p.first, c))
        }
    }
    // Using our new convention of multi-variable arguments...
    fn opdist(a: A, e: Either<B, C>) -> Either<Pair<A, B>, Pair<A, C>> {
        match e {
            Left(b) => Left(Pair(a, b)),
            Right(c) => Right(Pair(a, c))
        }
    }
    ```
3. It turns out that this `opdist` not only is a program that we cannot write in terms of categorical semantics, but it is the *only* program that we miss! If we have this program, then actually we *can* model the `match` construct exactly as it works in Rust, where indeed I can refer to previously declared variables.

# Exponential objects

In the previous lecture, we saw how products gave us a way to "pair" two objects. Today, we'll see how we can represent "functions" between objects categorically.

### Definition: exponential object $B^A$

Fix two objects $A, B$ in a category with products. These will stay fixed for the rest of the definition.

---

An object $E$ is said to be *an exponential object from $A$ to $B$* (notation: $B^A$) if the following conditions are satisfied:

1. *(Existence of evaluation.)* you must pick an arrow
   - $\textsf{eval} : E \times A \to B$

2. *(Existence of lambda abstraction.)* if someone gives you the following data,
   - another object $H$,
   - an arrow $f : H \times A \to B$,

   then you must pick an arrow
   - $\Lambda(f) : H \to E$,

   such that this equation holds, which we will call the *Lambda equation*:
   - $\langle \Lambda(f) \times \textsf{id}_A \rangle \,; \textsf{eval} = f$

   Here, $\Lambda(f) \times \textsf{id}_A : H \times A \to E \times A$ is the arrow that applies $\Lambda(f)$ to the first component of the pair and leaves the second component unchanged.

3. *(Eta uniqueness.)* the choice for $\Lambda$ that you gave above must satisfy the following equation, for every object $H$ and every arrow $h : H \to E$:

    $$\Lambda(\langle h \times \textsf{id}_A \rangle \,; \textsf{eval}) = h$$

    This property is called eta-expansion because it corresponds to the eta-equivalence rule in lambda calculus, which states that any function $f$ is equivalent to $\Lambda x. f(x)$. In categorical terms, if we take a function $h$ and apply it to an argument and then abstract over that argument again, we get back the original function.

> *Claim.* The choice for $\Lambda$ that you gave above must be unique. That is, for every object $H$ and arrow $f : H \times A \to B$, if there is another arrow $g : H \to E$ that satisfies the lambda equation, then $g = \Lambda(f)$.

**(End of definition.)**

Intuitively, the exponential object $B^A$ represents "the space of all functions from $A$ to $B$". The evaluation arrow lets us apply a function to an argument, and lambda abstraction lets us "curry" a function of two arguments into a function that returns another function.

## Examples of Exponential Objects: $\text{Prog}$

Let's show that the category $\text{Prog}$ has all exponential objects. Remember that $\text{Prog}$ has Rust types as objects and total programs as arrows.

*Proof.*

- *(Choice of $E$.)* For any two types `A`, `B`, we say that there is an object which satisfies the property of being an exponential object from `A` to `B`. The object we select for $E$ is `Func<A,B>`, the function type from `A` to `B`.

- *(Existence of evaluation.)* We need to provide a program `fn eval(f: Func<A,B>, a: A) -> B`, i.e., construct a Rust program with the given signature. We define it like this:

    ```rust
    fn eval(p: Pair<Func<A,B>, A>) -> B {
        (p.first)(p.second)
    }
    // equivalently...
    fn eval(f: Func<A,B>, a: A>) -> B {
        f(a)
    }
    ```

- *(Existence of lambda abstraction.)* Assume someone gives us a type `H` and a program:

    ```rust
    fn f(p: Pair<H, A>) -> B => ...
    ```

    The lambda abstraction arrow `lambda_f` for `f` is defined as follows:

    ```rust
    fn lambda_f(v: H) -> Func<A,B> {
        |a: A| f(Pair(v, a))
    }
    ```

    Let's verify the lambda equation:
    $\langle \Lambda(f) \times \textsf{id}_A \rangle \,; \textsf{eval} = f$

    The composed arrow is this program:
        ```rust
        fn compose_lambda_f_eval(p: Pair<H, A>) -> B {
            eval(Pair(lambda_f(p.first), p.second))
        }
        ```

    Let's reason by program equivalence, for some argument `p: Pair<H, A>`:
    ```rust
    eval(Pair(lambda_f(p.first), p.second))  // (Function expansion)
    = eval(Pair(|a: A| f(Pair(p.first, a)), p.second))  // (Function expansion)
    = (|a: A| f(Pair(p.first, a)))(p.second)  // (Function application)
    = f(Pair(p.first, p.second))  // (Pair expansion)
    = f(p)
    ```

    - *(Eta uniqueness.)* Assume we have a program `h: H -> Func<A,B>`, we show that $h$ is program equivalent to $h = \Lambda(\langle h \times \textsf{id}_A \rangle \,; \textsf{eval})$.
    The second program is the following, for some value `v:H`:

    ```rust
          v.lambda_(compose_(pairing_h_id)_eval)
        = |a: A| compose_(pairing_h_id)_eval(Pair(v, a))
        = |a: A| Pair(h(v), id(a)).eval()
        = |a: A| Pair(h(v), a).eval()
        = |a: A| h(v)(a)
        = h(v)
    ```

    Since the functions agree on all inputs, they are equivalent.

## Exponential Objects represent Function Types

Just as products in a category correspond to pairs in a programming language, exponential objects correspond to function types.

If you tell me that a category $C$ has all products and all exponential objects, then:
- The term language of $C$ is extended to have function types `Func<A,B>` for every type `A`, `B`
- Function application `f(x)` corresponds to the evaluation arrow
- Lambda abstractions `|x| e` correspond to the lambda abstraction arrow
- The beta-reduction rule in lambda calculus corresponds to the lambda equation

Consider the translation:

```rust
// Lambda abstraction
|x: A| e[x]
```

can be replaced by

```rust
lambda_expr[free variables]
```

The lambda equation is precisely the beta-reduction rule from lambda calculus:
```
(|x| e)(a) = e[a/x]
```

## Examples of Exponential Objects: $(\N, \le)$

Recall that in $(\N, \le)$, the product of two numbers $A$ and $B$ is $\min\{A, B\}$.

Question: Does this category have exponential objects?

Let's think about what an exponential object $B^A$ would mean in this context:

1. It needs to be a number $E$ such that there's an arrow $\textsf{eval} : E \times A \to B$
2. For any $H$ with an arrow $f : H \times A \to B$, there must be an arrow $\Lambda f : H \to E$

Since this is a preorder, arrows are just the $\le$ relation, and the exponential object corresponds to "implication" in this order.

In $(\N, \le)$, we need to find a number $E$ such that:
- $\min(E, A) \le B$
- For any $H$ where $\min(H, A) \le B$, we have $H \le E$

$E$ must be the "largest" number such that $\min(E, A) \le B$.

If $A \le B$, then we can take $E = \infty$ (or a sufficiently large number).
If $A > B$, there's no valid choice for $E$.

Therefore, exponential objects don't always exist in $(\N, \le)$.

## Examples of Exponential Objects: $(\text{BExpr}, \to)$

In boolean logic, the product corresponds to conjunction (AND). The exponential object corresponds to logical implication.

For propositions $A$ and $B$, the exponential object $B^A$ is $A \Rightarrow B$ (read: "A implies B").

1. *(Existence of evaluation.)* The arrow $\textsf{eval} : (A \Rightarrow B) \times A \to B$ corresponds to modus ponens:
   - If I know "A implies B" and I know "A", then I can deduce "B"

2. *(Existence of lambda abstraction.)* If for some proposition $H$, I know that $H \land A$ implies $B$, then $H$ implies $(A \Rightarrow B)$

The truth table for implication is:

| A | B | A ⇒ B |
|---|---|-------|
| 0 | 0 |   1   |
| 0 | 1 |   1   |
| 1 | 0 |   0   |
| 1 | 1 |   1   |

## Cartesian Closed Categories (CCCs)

A category is called *Cartesian closed* if:
1. It has a terminal object (we'll define this next lecture)
2. It has all binary products
3. It has all exponential objects

Cartesian closed categories are particularly important in computer science because they provide a mathematical foundation for typed lambda calculi and functional programming languages. In particular:

- Objects correspond to types
- Arrows correspond to functions
- Products correspond to pair types
- Exponential objects correspond to function types

The category $\text{Prog}$ of Rust types and total programs is Cartesian closed, as we've shown that it has all products and exponential objects.

## Why are they called exponential objects?

In $\text{FinProg}$, the subcategory with finite types:

```rust
// Consider these two types:
enum Three {
    A1, A2, A3
}

enum Two {
    B1, B2
}

enum TwoToThree = Func<Two,Three>

// 3^2 = 9
// |a| match a { B1 => A1, B2 => A1 }
// |a| match a { B1 => A1, B2 => A2 }
// |a| match a { B1 => A1, B2 => A3 }
// |a| match a { B1 => A2, B2 => A1 }
// |a| match a { B1 => A2, B2 => A2 }
// |a| match a { B1 => A2, B2 => A3 }
// |a| match a { B1 => A3, B2 => A1 }
// |a| match a { B1 => A3, B2 => A2 }
// |a| match a { B1 => A3, B2 => A3 }

enum ThreeToTwo = Func<Three,Two>

// 2^3 = 8
// |a| match a { A1 => B1, A2 => B1, A3 => B1 }
// |a| match a { A1 => B1, A2 => B1, A3 => B2 }
// |a| match a { A1 => B1, A2 => B2, A3 => B1 }
// |a| match a { A1 => B1, A2 => B2, A3 => B2 }
// |a| match a { A1 => B2, A2 => B1, A3 => B1 }
// |a| match a { A1 => B2, A2 => B1, A3 => B2 }
// |a| match a { A1 => B2, A2 => B2, A3 => B1 }
// |a| match a { A1 => B2, A2 => B2, A3 => B2 }
```

If $\textsf{size}(A) = n$ and $\textsf{size}(B) = m$, then:
- $\textsf{size}(B^A) = m^n$

For example, if $A$ has 2 elements and $B$ has 3 elements, then the number of possible functions from $A$ to $B$ is $3^2 = 9$.

This is exactly the number of elements in the function type `Func<A,B>`.
