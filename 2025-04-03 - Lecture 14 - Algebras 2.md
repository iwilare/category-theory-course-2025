# 2025-04-03 - Lecture 14 (Algebras)

## Main example: `ListInt` as initial algebra.

The recursive data type that we will look at now:
```rust
enum ListInt {
    Empty,
    Element(i32, ListInt)
}
```

---

```rust
enum ListIntAlg<X> {
    AlgEmpty,
    AlgElement(i32, X)
}
```
What does an algebra for this functor essentially provide?

> RECALL:
>
> In any category $C$, being initial is a universal property: if you give me two objects $I,I'$ which are both initial, then they are isomorphic.

*(Definition.)* An *initial algebra for $F$* is simply an initial object in $\text{Alg}(F)$.

*(Consequence.)* Every initial algebra is unique up to isomorphism in $\text{Prog}$..

---

Take this endofunctor of $\text{Prog}$, which is going to be super-important in the next section:

```rust
enum ListIntAlg<X> {
    AlgEmpty,
    AlgElement(i32, X)
}

type ListIntAlg<X> = Either<Unit, Pair<i32, X>>
// ListIntAlg(X) := 1 + i32 × X

// We show that it's a functor: as usual, assume fn f(a: A) -> B.
// Crucial: this function is not recursive! So it can be expressed in the term language.
fn ListInt_arrows_f(a: ListIntAlg<A>) -> ListIntAlg<B> {
    match a {
        AlgEmpty => AlgEmpty,
        AlgElement(v: i32, x: A) -> AlgElement(v, f(x))
    }
}
```

What does an algebra for this functor essentially provide?

An algebra for this functor is a choice of a type `A` with an arrow/program `fn extract(a: ListIntAlg<A>) -> A`, which essentially gives you a pair of functions like these:

```rust
fn extract_AlgEmpty(a: Unit) -> A { extract(AlgEmpty) } // equivalently,
fn extract_AlgEmpty() -> A { extract(AlgEmpty) }
fn extract_AlgElement(v: i32, a: A) -> A { extract(AlgElement(v, a)) }
```

where note that the first function is essentially just a value of `A`.

> Hence, an algebra for ListIntAlg consists of:
> - a choice of a type `A`,
> - a value of type `A`,
> - a function with signature `fn f(v: i32, a: A) -> A { ... }`

Now, time for something related.

---
---

## RECALL: the `foldr` function

*If you have seen this idea before you can skip this section.*

There is a program `fold`, parametric for every `A`. The idea is that given a list like this:

$$[7,3,8,2]$$

1. How do I compute the sum of this list?

You pick a program $\otimes$ that takes two inputs, and you pick a value $z$. The `fold` function, if you choose $\otimes$ and $z$, does essentially this:

$$7 \otimes (3 \otimes (81 \otimes (2  \otimes z))).$$

In this case, the $ \otimes $ program must be able to take an integer as one of the arguments, and then it can return whatever it wants.

If I pick $\otimes$ to do the sum of its arguments, and I pick $z = 0$, then I obtain the sum.

```rust
fn otimes(a: i32, b: i32) -> i32 {
    a + b
}
```

$$7 + (3 + (81 + (2  + 0))).$$

2. How do I know the length of a list?

If I pick $\otimes$ to ignore the argument on the left and just give the argument on the right +1,and I pick $z = 0$, then I obtain the length.

```rust
fn otimes(a: i32, b: i32) -> i32 {
    b + 1
}
```

$$1 + (1 + (1 + (1 + 0))).$$

3. I can actually end up in any possible type that I pick: how I do I write the function that converts the list above to string?

```rust
fn otimes(a: i32, b: String) -> String {
    to_string(a) + "," + b   // string concatenation
}
```

As z I must pick the empty string.

$$"7" + ("3" + ("81" + ("2" + ""))).$$
$$"73812"$$
$$"7,3,81,2,"$$

#### Most common way of presenting `foldr`:

The following code is the typical way in which `foldr` is represented (e.g., in Lean, Idris2, F#, Haskell):

```rust
fn foldr<A>(base: A, func: Func<Pair<i32, A>, A>, list: ListInt) -> A {
    match list {
        Empty => base,
        Element(v: i32, tail: ListInt) -> func(v, foldr(base, func, tail))
    }
}
```

An alternative "parametric" and **less functional** version of this program is the following process:

> If you give me these functions,
> 1. `fn base(_: Unit) -> A`, which is the same as just saying an element of `A`, just like `base: A`,
> 2. `fn func(v: i32, a: A) -> A`,
> then I can cook up a function
>
> ```rust
> fn foldr[base][func](list: ListInt) -> A {
>     match list {
>         AlgEmpty => base,
>         AlgElement(v, tail: ListInt) -> func(v, foldr[base][func](tail))
>     }
> }
> ```
> Where now `base` and `func` are not "arguments", but there is a different `foldr` function that I cook up for you for every possible choice of `base` and `func` that you give me.


The next one is the version that we will use here.
Let's write the above choice explicitly in terms of algebras of `ListIntAlg`.

Imagine that someone gives us an algebra for `ListIntAlg`, i.e., they pick a type `H` and gives us an arrow/program `fn alpha(a: ListIntAlg<H>) -> H`. Then, we can construct an arrow like this:

> ```rust
> fn foldr[alpha](list: ListInt) -> H {
>     match list {
>         Empty => alpha(AlgEmpty),
>         Element(v, tail: ListInt) -> alpha(AlgElement(v, foldr[alpha](tail)))
>     }
> }
> ```

## `ListInt` as initial algebra for `ListIntAlg`.

**Spoilers for what we will do**: we show that:
1. There is an algebra $i := $ (`ListInt`, `fn constr(a: ListIntAlg<ListInt>) -> ListInt`),
2. This algebra $i$ is an initial algebra in the category $\textsf{Alg}(\texttt{ListIntAlg})$.
3. The arrow `constr` gives you the constructors of `ListInt`,
4. The initiality property of `ListInt` tells you precisely how to define `foldr`, in a unique way.

---

1. First, we decide the arrow `fn constr(a: ListIntAlg<ListInt>) -> ListInt` of the algebra $i$:

    Again, if we've done our calculations correct in the previous section, this should amount to:
    1. A value of `ListInt`,
    2. A binary function that takes as input an integer and a value of `ListInt` to produce a value of `ListInt`.

    ```rust
    enum ListIntAlg<X> {
        AlgEmpty,
        AlgElement(i32, X)
    }

    fn constr(a: ListIntAlg<ListInt>) -> ListInt {
        match a {
            AlgEmpty => Empty,
            AlgElement(v: i32, a: ListInt) => Element(v, a)
        }
    }

    // So, yes! The two choices for values and binary function are precisely the constructors of ListInt.
    ```
2. We show that this algebra is initial in the category $\textsf{Alg}(\texttt{ListIntAlg})$:

    What does it mean to be initial?

    For every other algebra $(H,\alpha) := (H, \texttt{fn alpha(a: ListIntAlg<H>) -> H})$, there is an algebra arrow $f : i \to (H,\alpha)$.

    What is an algebra arrow again? It's an arrow of $\text{Prog}$ from the object chosen by $i$ to the object chosen by $\alpha$:

    It's a standard arrow from the object of the first algebra, so `ListInt`, to the object of the second algebra, so `H`.
    That is, we need to construct an arrow `f` that has precisely the signature of `foldr`!

    So, we choose `foldr` as our arrow $f$.

    But for this to really be an algebra arrow, it needs to make this square commute, otherwise it's not.

    $$
    \begin{array}{ccccccccc}
    \phantom{\alpha} F(X) & \xrightarrow{F(f)} & F(Y) \phantom{\beta} & \\
    {\alpha} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {\beta}\\
    \phantom{\alpha} X & \xrightarrow{\phantom{iii}f\phantom{iii}} & Y \phantom{\beta} \\
    \end{array}
    $$

    The situation is this one:

    $$
    \begin{array}{ccccccccc}
    \phantom{\texttt{constr}} \texttt{ListIntAlg}(\texttt{ListInt}) & \xrightarrow{\texttt{ListIntAlg}(\texttt{foldr}_\alpha)} & \texttt{ListIntAlg}(H) \phantom{\alpha} & \\
    {\texttt{constr}} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {\alpha}\\
    \phantom{\texttt{constr}} \texttt{ListInt} & \xrightarrow{\phantom{iii}\texttt{foldr}_\alpha\phantom{iii}} & H \phantom{\alpha} \\
    \end{array}
    $$

    Let's verify that the square commutes; remember that $\texttt{ListIntAlg}(\texttt{foldr}_\alpha)$ corresponds to the program on arrows of $\texttt{foldr}_\alpha$.

    The square commuting, in $\texttt{Prog}$, means that two arrows/programs are program equivalent.
    In particular, we need to check that for every value `v: ListIntAlg<ListInt>` the following two programs are equivalent:

    ```rust
        alpha(ListIntAlg_arrows_foldr(v))
      = foldr(constr(v))
    ```

    where we don't know anything about `fn alpha(v: ListIntAlg<H>) -> H` and the other arrows are defined like this:

    ```rust
    fn ListIntAlg_arrows_f(a: ListIntAlg<A>) -> ListIntAlg<B> {
        match a {
            AlgEmpty => AlgEmpty,
            AlgElement(v: i32, x: A) -> AlgElement(v, f(x))
        }
    }
    // so, for f=foldr,
    fn ListIntAlg_arrows_foldr(a: ListIntAlg<ListInt>) -> ListIntAlg<H> {
        match a {
            AlgEmpty => AlgEmpty,
            AlgElement(i: i32, x: ListInt) -> AlgElement(i, foldr(x))
        }
    }
    fn foldr[alpha](a: ListIntAlg<ListInt>) -> ListIntAlg<H> {
        match a {
            Empty => alpha(AlgEmpty),
            Element(i: i32, v: ListInt) -> alpha(AlgElement(v, foldr[alpha](v)))
        }
    }
    fn constr(a: ListIntAlg<ListInt>) -> ListInt {
        match a {
            AlgEmpty => Empty,
            AlgElement(v: i32, a: ListInt) => Element(v, a)
        }
    }
    ```

    There are two cases to check:
    1. `v = AlgEmpty`:
        ```rust
           alpha(ListIntAlg_arrows_foldr(v))
         = alpha(ListIntAlg_arrows_foldr(AlgEmpty))
         = alpha(AlgEmpty)
        ```
        The other side of the equality,
        ```rust
          foldr(constr(v))
        = foldr(constr(AlgEmpty))
        = foldr(Empty)          // this is the point which we impose to be = alpha(AlgEmpty).
        = alpha(AlgEmpty)
        ```

    2. `v = AlgElement(i, x)` for a value `i: i32` and a list `x: ListInt`, we must have that
        ```rust
           alpha(ListIntAlg_arrows_foldr(v))
         = alpha(ListIntAlg_arrows_foldr(AlgElement(i, x)))
         = alpha(AlgElement(i, foldr(x)))
        ```
        The other side of the equality,
        ```rust
           foldr(constr(v))
         = foldr(constr(AlgElement(i, x)))
         = foldr(Element(i, x)) // this is the point where we impose = alpha(AlgElement(i, foldr(x))).
         = alpha(AlgElement(i, foldr(x)))
3. *(This last point does not need to be studied in detail, since we do not introduce/assume that you know about induction.)* Given any other algebra arrow $h : (\texttt{ListInt},\texttt{constr}) \to (H,\alpha)$ (so, an arrow `fn h(a: ListInt) -> H` which makes the algebra arrow square commute), we need to show that `h` is actually program equivalent to $\textsf{foldr} : (\texttt{ListInt},\texttt{constr}) \to (H,\alpha)$. This is uniqueness of the arrow from initial objects.

    > Remember that since we know that `h` is also an algebra arrow, we know that the equation
    > ```rust
    >     alpha(ListIntAlg_arrows_h(v)) // note that here we have `h`!
    >   = h(constr(v))
    > ```
    > is true for every value `v: ListIntAlg<ListInt>`.
    > We showed that `foldr` a similar equation also holds (either we define `foldr` using this equation as guideline or just define it as we already know and then prove that this equation actually holds):
    > ```rust
    >     alpha(ListIntAlg_arrows_foldr(v))
    >   = foldr(constr(v))
    > ```

    By program equivalence, we want to show that, for every value `v: ListInt`, `h(v) = foldr(v)`.
    In order to do this, we need to rely on the fact that we can do *induction* on lists and prove properties about lists using induction.

    1. When `v = Empty`, the two functions agree:
        ```rust
           h(Empty)
         = h(constr(AlgEmpty))                      // now, since h is an algebra arrow,
         = alpha(ListIntAlg_arrows_h(AlgEmpty))     // by definition of ListIntAlg_arrows_h,
         = alpha(AlgEmpty)                          // by definition of ListIntAlg_arrows_foldr,
         = alpha(ListIntAlg_arrows_foldr(AlgEmpty)) // now, since foldr is an algebra arrow,
         = foldr(constr(AlgEmpty))
         = foldr(Empty)
         ```
    2. When `v = Element(i, x)` for some values `i: i32` and `x: ListInt`,
        ```rust
           h(Element(i, x))
         = h(constr(AlgElement(i, x)))                  // now, since h is an algebra arrow,
         = alpha(ListIntAlg_arrows_h(AlgElement(i, x))) // definition of ListIntAlg_arrows_h,
         = alpha(AlgElement(i, h(x)))                   // by *inductive hypothesis*, h(x) = foldr(x), since x is smaller than v,
         = alpha(AlgElement(i, foldr(x)))               // definition of ListIntAlg_arrows_foldr,
         = alpha(ListIntAlg_arrows_foldr(AlgElement(i, x))) // now, since foldr is an algebra arrow,
         = foldr(constr(AlgElement(i, x)))
         = foldr(Element(i, x))
        ```
    So, the above argument is true essentially because we can case split on the two possible cases of list constructors and do induction (this is a peculiar feature of $\texttt{Prog}$!); the rest simply follows automatically from the algebra arrow equation that both arrows have.

    *(Claim).* Clearly, being able to do induction on lists allows us to prove this. Actually, the inverse is also true: if we know that this equation holds, then we're able to show that ANY two arrows/programs defined on lists are equal whenever they are *inductively* equal (essentially because `f = foldr = g` for any `f`, `g`).



# `ListInt` is a fixpoint of the functor `ListInt`.

*(Claim.)* $\texttt{constr}$ is actually an isomorphism, and $\texttt{ListInt} \cong \texttt{ListIntAlg<ListInt>}$, or, in other words, $\texttt{ListInt}$ is a fixpoint of the functor $\texttt{ListIntAlg}$ up-to-isomorphism.

### We will do this in general for any `F` in any category.

# Lambek's Theorem: The arrows inside initial algebras for any endofunctor are isomorphisms.

*(Theorem.)* Take any category $C$ and an endofunctor $F : C \Rightarrow C$.

Assume there exists an initial algebra $(I, \gamma : F(I) \to I)$. (note that $I$ is not the initial object of $C$ here!!)

*(Claim.)* Then, $\gamma$ is part of the isomorphism $F(I) \cong I$, i.e., there is an arrow $\beta : I \to F(I)$ which composes to the identity with $\gamma$ in both ways.

Again, saying that $F(I) \cong I$ essentially amounts to saying that $I$ is a fixpoint of $F$.

This is a consequence of initiality; if we had *weak initality* we would not be able to claim this.

*Notation.* we choose here, as we did in the lecture on initality, to call with $!_\mathcal{A}$ the algebra arrow from the initial algebra to any other algebra $\mathcal{A}$, consequence of initiality.

1. First, we construct this very peculiar algebra, call it $\mathcal{A} = (F(I), \gamma : F(F(I)) \to F(I))$: the object that we pick as $X$ is $F(I)$, and the arrow $F(X) \to X$ is this one:

    $$
    \begin{array}{ccccccccc}
    \phantom{F(\gamma)} F(F(I)) \\
    {F(\gamma)} \downarrow \\
    \phantom{F(\gamma)} F(I) & \\
    \end{array}
    $$

   This is a perfectly fine algebra, we built an arrow with the required type.

1. Notice, by initiality of $(I,\gamma)$, that there must be an arrow $!_{\mathcal{A}} : I \to F(I)$ which is an algebra arrow, like this:

    $$
    \begin{array}{ccccccccc}
    \phantom{\gamma} F(I) & \xrightarrow{F(!_{\mathcal{A}})} & F(F(I)) \phantom{F(\gamma)} & \\
    {\gamma} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {F(\gamma)}\\
    \phantom{\gamma} I & \xrightarrow{\phantom{iii}!_{\mathcal{A}}\phantom{iii}} & F(I) \phantom{F(I)} \\
    \end{array}
    $$
    Look! This $!_{\mathcal{A}}$ is a candidate for our inverse arrow to $\gamma$ that composes to the identity.
3. NOTICE, that there is another algebra arrow! It goes from $F(I) \to I$ and it makes the square commute.

    $$
    \begin{array}{ccccccccc}
    \phantom{\gamma} F(I) & \xrightarrow{F(!_{\mathcal{A}})} & F(F(I)) \phantom{F(\gamma)} & \xrightarrow{F(\gamma)} & F(I) \\
    {\gamma} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {F(\gamma)} & \raisebox{2pt}{\tiny =} & \downarrow {\gamma}\\
    \phantom{\gamma} I & \xrightarrow{\phantom{iii}!_{\mathcal{A}}\phantom{iii}} & F(I) \phantom{F(I)} & \xrightarrow{\phantom{iii}{\gamma}\phantom{iii}} & I\\
    \end{array}
    $$

    It's an algebra arrow because the square on the right commutes pretty trivially (the two composite arrows are literally the same)

    However, this is another algebra arrow from, again, the initial object into itself: the identity algebra arrow!!

    Hence, the composition $!_{\mathcal{A}} \,; \gamma = \textsf{id}_I$ is an algebra arrow because it makes the big square/rectangle (rectangle = pasting of two squares) commute.

    $$
    \begin{array}{ccccccccc}
    \phantom{\gamma} F(I) & \xrightarrow{F(\textsf{id}_{I})} & F(I) \phantom{\gamma} \\
    {\gamma} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {\gamma} \\
    \phantom{\gamma} I & \xrightarrow{\phantom{iii}\textsf{id}_I\phantom{iii}} & I \phantom{\gamma}\\
    \end{array}
    $$

    Since $F(\textsf{id}_{I}) = \textsf{id}_{F(I)}$, the square commutes and so this is also an algebra arrow. (we did this when we checked that $\textsf{Alg}(F)$ is a category.)

    BUT, by uniqueness, we must have that $!_{\mathcal{A}} \,; \gamma = \textsf{id}_I$, because these are two "distinct" *algebra arrows* from the same initial object, and since we assume that $(I, \gamma)$ is the initial object there can only be one. (this step is analogous to when we showed that being initial is a universal property.)

4. In the other direction, we want to show that $\gamma \,; !_\mathcal{A} = \textsf{id}_{F(I)}$. This actually follows very quickly from the commutativity of this square that we invoked before using initiality:
    $$
    \begin{array}{ccccccccc}
    \phantom{\gamma} F(I) & \xrightarrow{F(!_{\mathcal{A}})} & F(F(I)) \phantom{F(\gamma)} & \\
    {\gamma} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {F(\gamma)}\\
    \phantom{\gamma} I & \xrightarrow{\phantom{iii}!_{\mathcal{A}}\phantom{iii}} & F(I) \phantom{F(I)} \\
    \end{array}
    $$

    This, equationally, states that

    $$F(!_\mathcal{A}) \,; F(\gamma) = \gamma \,; !_\mathcal{A}
    $$

    Using functoriality,

    $$F(!_\mathcal{A} \,; \gamma) = \gamma \,; !_\mathcal{A}
    $$

    But by point 3. we know that the two arrows inside $F$ compose to the identity on $I$,

    $$F(\textsf{id}_I) = \gamma \,; !_\mathcal{A}
    $$

    and since functors preserve identities,

    $$\textsf{id}_{F(I)} = \gamma \,; !_\mathcal{A}.
    $$
    $\square$

# Back to $\texttt{ListInt}$, what does Lambek tell us about it?

What does this say in our case? That $$\texttt{ListInt} \cong \texttt{ListIntAlg<ListInt>}$$
i.e., that
$$\texttt{ListInt} \cong \texttt{1 + (i32 × ListInt)}$$
If you take this literally, then:

$$
\begin{array}{lll}
\texttt{ListInt} & \cong & \texttt{1 + i32 × ListInt} \\
& \cong &\texttt{1 + i32 × (1 + i32 × ListInt)} \\
& \cong &\texttt{1 + i32 × 1 + i32 × i32 × ListInt} \\
& \cong &\texttt{1 + i32 + i32 × i32 × ListInt} \\
& \cong &\texttt{1 + i32 + i32 × i32 × (1 + i32 × ListInt)} \\
& \cong &\texttt{1 + i32 + i32 × i32 × 1 + i32 × i32 × i32 × ListInt} \\
& \cong &\texttt{1 + i32 + i32 × i32 × 1 + i32 × i32 × i32 × 1 + i32 × i32 × i32 × ListInt} \\
& \cong &\texttt{1 + $\underbrace{\texttt{i32}}{}$ + $\underbrace{\texttt{i32 × i32}}{}$ + $\underbrace{\texttt{i32 × i32 × i32}}{}$ + $\underbrace{\texttt{i32 × i32 × i32 × i32}}{}$ + ...} \\
\end{array}
$$

# Now, you can do this exact process about every single recursive algebraic data type that you can think of: each type is going to be the object of an initial algebra.

# Something that is not a functor...


We need to add function types in the mix for us to be able to create something that is not a functor. if we onl have coproducts (Either), products (Pairs), terminals (Unit), initial (Empty) then everything we can build out of these is going to be a functor and the recursive datatypes that you build out of the initial algebra process are effectively the correct ones.

An example of something that is not a functor:

```rust
struct NotAFunctor<X> {
    endo: Func<X, X>
}
type NotAFunctor<X> = Func<X, X>

// Whenever we have given an fn f(a: A) -> B { ... }
// We cannot complete this definition, however:
fn NotAFunctor_arrow_f(a: NotAFunctor<A>): NotAFunctor<B> {
    ...
}
fn NotAFunctor_arrow_f(a: Func<A,A>): Func<B,B> {
    let g: Func<A,B> = compose(a, f)
}
```




# On the term language

Take any category $C$ with products, coproducts, and a terminal object. Pick a special object $V$ of $C$, which acts as our `i32`.

Consider the endofunctor $LA$ defined like this:
- the program on object sends $X$ to $1 + V \times X$,
- the program on arrows sends an arrow $f : A \to B$ to an arrow $LA(f) : (1 + V \times A) \to (1 + V \times B)$, which is defined in a suitable way which we omit here.

*(Claim.)* If the category of algebras of $\textsf{Alg}(LA)$ has an initial object $(L, c : LA(L) \to L)$, then the term language comes equipped with:
- A type `List<V>` (corresponding to the object $L$),
- A constructor `Empty` (corresponding to $\textsf{inl}\,;\,c$)
- A constructor `Element` (corresponding to $\textsf{inr}\,;\,c$)
- The possibility of simple recursion on lists; simple recursion here essentially means that you're only allowed to call recursive functions on the *tail* of the list. More complicated forms of recursions are not too hard to obtain, but require more care.
*(Claim.)* Every function definable in the above Rust-like term language can be written in terms of `foldr`.

# Small extra: coinduction

We have seen *initial algebras*: in the case of a certain functor `ListIntAlg`, the type `ListInt` with its constructors becomes an initial algebra.

What if we reverse all the arrows?

In other words, what is a *terminal coalgebra* for `ListIntAlg`?
The answer turns out to be this: such terminal coalgebras do not exist in the version of Rust that we talk about, and actually they do not arise (in a precise way) in most programming languages, with the notable exception of *Idris, Haskell*, and Java to some extent.

*(Claim.)* The terminal coalgebra for `ListIntAlg` is `ColistInt` (if you've seen Idris then you know what this is.) `ColistInt` is defined as follows:

```rust
type ColistInt = Either<ListInt, StreamInt>
```

where now `StreamInt` is the type of *infinite streams of integers*.

*(Claim.)* Moreover, `StreamInt` is also a terminal coalgebra. Take this very simple functor here:

```rust
enum StreamInt {
    // No case of empty! Streams do not ever terminate.
    Element(a: i32, v: StreamInt)
}
enum StreamIntAlg<X> {
    Element(a: i32, v: X)
}

type StreamIntAlg<X> = Pair<i32, X>

// Not obvious how to create a value like this in a finite amount of time... (unless you have pointers)
```
The claim is that, if Rust had full coinductive datatypes, then `StreamInt` (with the constructor above) would be a terminal coalgebra for this functor `StreamIntAlg`.

Notice the duality:
- Induction was used to prove uniqueness of the arrow from the initial algebra to any other one in the case of lists.
- Coinduction will need to be used to prove uniqueness of the arrow into the terminal algebra from any other one in the case of lists.
