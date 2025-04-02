# 2025-03-14 - Lecture 9 (Term language, exponential objects)

## Today's plan

- Introduce the definition of exponential object
- See examples of exponential objects in two of the categories we have seen

# Back on term language:

At the beginning we saw that we restricted our Rust programs to only programs that take a single argument (and that have no effects). We will not lift this restriction to effects yet, but we will expans a little bit our language possibilities: the idea is that certain categorical features allow us to equivalently "translate"/compile these extra features in terms of such structure, and therefore allow us to go back to the original restrictions where, e.g., each Rust program must still take a single argument.


1. *Claim.* If $C$ is a category with *a product  $P$ for every $A$,$B$*, then the term language is allowed to:
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

    // For any H,
    // ()_H : H -> Unit
    // ()_A : A -> Unit
    // ()_B : B -> Unit

    // equivalently:
    fn do_computation(a: A) {
        ...
    }
    // same program, same source code:
    fn do_computation(b: B) {
        ...
    }
    // ...but then `b` is ignored.
    ```

    **Example of translation/compilation from the term language into category theory and viceversa**:

    ```rust
    fn special1(p: Pair<A,Pair<B,A>) -> Pair<A,B> {
        Pair { first: p.first, second: p.second.first }
    }
    fn special2(p: Pair<A,Pair<B,A>) -> Pair<A,B> {
        Pair { first: p.second.second, second: p.second.first }
    }
    ```
    Equivalent arrows in "math" notation:

    $$
        \textsf{special1} : A \times (B \times A) \longrightarrow A \times B \\
        \textsf{special2} : A \times (B \times A) \longrightarrow A \times B \\

        \textsf{special1} := \langle \textsf{fst} , \textsf{snd}\,;\textsf{fst} \rangle \\
        \textsf{special2} := \langle \textsf{snd}\,;\textsf{snd}  , \textsf{snd}\,;\textsf{fst} \rangle \\
            $$
    In a sense, projections also discarded data:
    $$        \textsf{fst} : A \times B \longrightarrow A \\
            \textsf{snd} : A \times B \longrightarrow B \\
            ()_{A \times B} : A \times B \longrightarrow 1
    $$

    *Important point.* Ignoring values can only be done because of the existence of terminal objects.
    If you look at the term language for general categories that was introduced in Lecture 4, the argument cannot be discarded!
3. *Claim.* If $C$ is a category with an *initial object* $A$, called "`Empty`", then the term language is allowed to:
    - have a type `Empty`,
    - use the "destructor"/pattern matching `match e { }` (no clauses!)
    - **In particular**, this allows for programs to "not return a value"/return *into any other type*.
    *Translation idea:*
    ```rust
    // A function which never returns

    // Equivalent way of writing this program,
    // assuming that fn f(a: A) -> Empty
    fn program(a: A) -> B {
        return !_B(f(a))
    }
    fn program(a: A) -> B {
        ...
        let e: Empty = f(...)
        match e {

        }
    }
    fn program(a: A) -> C {
        ...
        let e: Empty = f(...)
        match e {

        }
    }
    ```
    (Side remark for the curious: having *some* effects is consistent with having an initial object.)

    ```rust
    fn prog1(a: A) -> Empty {
        while true {
                print("Hellow")
        }
    }
    fn prog2(a: A) -> Empty {
        while true {
                print("World")
        }
    }
    ```

    (End of side-remark.)


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

### Definition: exponential object $A \Rightarrow B$

Fix two objects $A, B$ in a category with products. These will stay fixed for the rest of the definition.

---

An object $E$ is said to be *an exponential object from $A$ to $B$* (notation: $A \Rightarrow B$ or $B^A$) if the following conditions are satisfied:

1. *(Existence of evaluation.)* you must pick an arrow
   - $\textsf{eval} : (A \Rightarrow B) \times A \to B$

2. *(Existence of lambda abstraction.)* if someone gives you the following data,
   - another object $H$,
   - an arrow $f : H \times A \to B$,

   then you must pick an arrow
   - $\Lambda(f) : H \to (A \Rightarrow B)$,

3. *(Lambda equation)* The choice above is such that this equation, called *Lambda equation* holds:
   $$(\Lambda(f) \times \textsf{id}_A) \,; \textsf{eval} = f$$

   Here, $\Lambda(f) \times \textsf{id}_A : H \times A \to (A \Rightarrow B) \times A$ is the arrow that applies $\Lambda(f)$ to the first component of the pair and leaves the second component unchanged.

   > Intuition, for every $v: H$, $a:A$:
   > $$\textsf{eval}((\texttt{fun }x\,\,\texttt{=>}\,f(v,x)), a) = f(v,a)$$
   > $$(\texttt{fun }x\,\,\texttt{=>}\,f(v,x))(a) = f(v,a)$$

4. *(Lambda expansion.)* the choice for $\Lambda$ that you gave above must satisfy the following equation, for every object $H$ and every arrow $h : H \to (A \Rightarrow B)$:

    $$\Lambda((h \times \textsf{id}_A) \,; \textsf{eval}) = h$$

    Where $h \times \textsf{id}_A : H \times A \to (A \Rightarrow B) \times A$.

    > Intuition:
    > $$(\texttt{fun }x\,\,\texttt{=>}\,h(x)) = h$$

> *Claim.* The choice for $\Lambda$ that you gave above is unique: that is, for every object $H$ and arrow $f : H \times A \to B$, if there is another arrow $g : H \to E$ that satisfies the lambda equation, then $g = \Lambda(f)$.

Other names: "Lambda equation" is sometimes called $\beta$-equivalence or $\beta$-reduction, and "Lambda expansion" sometimes is called $\eta$-equivalence or $\eta$-expansion, stating that any function $f$ is equivalent to $\lambda x. f(x)$.


**(End of definition.)**

Intuitively, the exponential object $A \Rightarrow B$ represents "the type of functions from $A$ to $B$". The evaluation arrow lets us apply a function to an argument, and lambda abstraction lets us create new functions out of functions that take new arguments.

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

- *(Lambda equation.*) Let's verify the lambda equation:
    $\langle \Lambda(f) \times \textsf{id}_A \rangle \,; \textsf{eval} = f$

    The composed arrow is this program:
        ```rust
        fn compose_(parallel_lambda_f_id)_eval(p: Pair<H, A>) -> B { ... }
        ```

    Let's reason by program equivalence, for some argument `p: Pair<H, A>`:
    ```rust
      compose_(parallel_(lambda_f)_id)_eval(p)
    = eval(Pair(lambda_f(p.first), id(p.second)))       // (Function expansion)
    = eval(Pair(|a: A| f(Pair(p.first, a)), p.second))  // (Function expansion)
    = (|a: A| f(Pair(p.first, a)))(p.second)            // (Function application)
    = f(Pair(p.first, p.second))                        // (Pair expansion)
    = f(p)
    ```

- *(Lambda expansion.)* Assume we have a program `h: H -> Func<A,B>`, we show that $h$ is program equivalent to $\Lambda(\langle h \times \textsf{id}_A \rangle \,; \textsf{eval}) = h$.
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

**In a sense we are "internalizing" the notion of arrow as an object inside the category itself.**

## Term Language: Exponential Objects represent Function Types

Just as products in a category correspond to pairs in a programming language, exponential objects correspond to function types.

If you tell me that a category $C$ has all products and all exponential objects, then:
- The term language of $C$ is extended to have function types `Func<A,B>` for every type `A`, `B`
- Function application `f(x)` corresponds to the evaluation arrow,
- Lambda abstractions `|x| e` correspond to the lambda abstraction arrow,
- Function application corresponds to the lambda equation.
- Lambda expansion is needed to show that function types are unique up to isomorphism.

Consider the translation:

```rust
// Lambda abstraction
|x: A| expr[a,b,...,z,x]
```

is replaced by

```rust
lambda_expr[a,b,...,z]
```

The lambda equation is precisely the beta-reduction rule from lambda calculus:
```
(|x| e[x])(a) = e[a]
```

## Examples of Exponential Objects: $(\text{BExpr}, \to)$

In boolean logic, the product corresponds to conjunction (AND). The exponential object corresponds to logical implication.

We add a new case to the old definition of `BExpr`.

```rust
enum BExpr {
    LessEq(String, i32),
    GreaterEq(String, i32),
    And(BExpr, BExpr),
    Or(BExpr, BExpr),
    Not(BExpr),
    F,
    T,
    // New case:
    Imply(BExpr, BExpr)
}
```

The intuition is that `Imply(P,Q)`, which we write as `P ⇒ Q`, is true exactly when the implication `P` $\to$ `Q` is true, according to the usual table:

| P | Q | P ⇒ Q |
|--|--|--|
| 0 | 0 | 1 |
| 0 | 1 | 1 |
| 1 | 0 | 0 |
| 1 | 1 | 1 |

**In a sense we are "internalizing" the notion of arrow as an object inside the category itself.**

For propositions $A$ and $B$, the exponential object $A \Rightarrow B$ is the boolean expression ``Imply(A,B)`` for some boolean expressions `A`, `B`.

1. *(Existence of evaluation.)* The arrow $\textsf{eval} : (A \Rightarrow B) \times A \to B$:
   - If I know `(A ⇒ B) && A`, then I can deduce `B`.

| A | B | (A ⇒ B) | (A ⇒ B) && A
|--|--|--|--|
| 0 | 0 | 1 | 0 |
| 0 | 1 | 1 | 0 |
| 1 | 0 | 0 | 0 |
| 1 | 1 | 1 | 1 |


2. *(Existence of lambda abstraction.)* If for some proposition $H$,
    - Assume that, knowing that `H && A` holds then `B` holds, (existence of the arrow $f$)
    - then I need to show that knowing that `H` holds then `(A ⇒ B)` holds. (existence of the arrow $\Lambda(f)$)

| H | A | H&A    | B |  A ⇒ B | `H&A` $\to$ `B`| `H` $\to$ `A ⇒ B` |
| - |---| -      |---| -------| -              | - |
| 0 | 0 | 0      | 0 |    1   | yes            | yes |
| 0 | 0 | 0      | 1 |    1   | yes            | yes |
| 0 | 1 | 0      | 0 |    0   | yes            | yes |
| 0 | 1 | 0      | 1 |    1   | yes            | yes |
| 1 | 0 | 0      | 0 |    1   | yes            | yes |
| 1 | 0 | 0      | 1 |    1   | yes            | yes |
| 1 | 1 | 1      | 0 |    0   | no             | no  |
| 1 | 1 | 1      | 1 |    1   | yes            | yes |

## Cartesian Closed Categories (CCCs)

A category is called *cartesian closed* if:
1. It has a terminal object
    - (i.e., there is an object $1$ that is terminal)
2. It has all products
    - (i.e., for any $A,B$ there exists an object $P$ that satisfies the property of being a product of $A,B$)
3. It has all exponential objects
    - (i.e., for any $A,B$ there exists an object $E$ that satisfies the property of being exponential for $A,B$)

Cartesian closed categories are particularly important in computer science because they provide a mathematical foundation for typed lambda calculi and functional programming languages.

The category $\text{Prog}$ of Rust types and total programs is cartesian closed whenever we allow for the types `Unit`, `Pair<A,B>`, `Func<A,B>`.

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
// |a: Two| match a { B1 => A1, B2 => A1 }
// |a: Two| match a { B1 => A1, B2 => A2 }
// |a: Two| match a { B1 => A1, B2 => A3 }
// |a: Two| match a { B1 => A2, B2 => A1 }
// |a: Two| match a { B1 => A2, B2 => A2 }
// |a: Two| match a { B1 => A2, B2 => A3 }
// |a: Two| match a { B1 => A3, B2 => A1 }
// |a: Two| match a { B1 => A3, B2 => A2 }
// |a: Two| match a { B1 => A3, B2 => A3 }

enum ThreeToTwo = Func<Three,Two>

// 2^3 = 8
// |a: Three| match a { A1 => B1, A2 => B1, A3 => B1 }
// |a: Three| match a { A1 => B1, A2 => B1, A3 => B2 }
// |a: Three| match a { A1 => B1, A2 => B2, A3 => B1 }
// |a: Three| match a { A1 => B1, A2 => B2, A3 => B2 }
// |a: Three| match a { A1 => B2, A2 => B1, A3 => B1 }
// |a: Three| match a { A1 => B2, A2 => B1, A3 => B2 }
// |a: Three| match a { A1 => B2, A2 => B2, A3 => B1 }
// |a: Three| match a { A1 => B2, A2 => B2, A3 => B2 }
```

If $\textsf{size}(A) = n$ and $\textsf{size}(B) = m$, then:
- $\textsf{size}(A \Rightarrow B) = m^n$

For example, if $A$ has 2 elements and $B$ has 3 elements, then the number of possible functions from $A$ to $B$, i.e., the type $A \Rightarrow B = B^A$ is $3^2 = 9$.

This is exactly the number of elements in the function type `Func<A,B>`.
