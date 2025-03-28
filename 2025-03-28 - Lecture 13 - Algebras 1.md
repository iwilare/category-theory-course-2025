# 2025-03-28 - Lecture 11 (Algebras)

# Motivation

Consider the following algebraic data type (non-recursive!)

```rust
enum Expr<A> {
    Const(i32),
    Add(A, A),
    Mul(A, A),
    Neg(A),
}

// Add(32, 6): Expr<i32>
// Const(7):   Expr<i32>
// Neg(42):    Expr<i32>

// Add("hello", "world"): Expr<String>
// Const(98):             Expr<String>
// Neg("asd"):            Expr<String>
```

`Expr` is a functor, defined by sending `X` to `Expr<X>`.
Let's see the program on arrows. Assume to have an arrow `fn f(a: A) -> B`, then,

```rust
fn Expr_arrow_f(a: Expr<A>) -> Expr<B> {
    match a {
        Const(v) -> Const(v)
        Add(a1, a2) -> Add(f(a1), f(a2)),
        Mul(a1, a2) -> Mul(f(a1), f(a2))
        Neg(a) -> Neg(f(a))
    }
}
```

Now, we would like to extract a concrete value out of these expressions:

```rust
fn extract_value(a: Expr<i32>) -> i32 {
    match a {
        Const(v) -> v,
        Add(a1, a2) -> a1 + a2,
        Mul(a1, a2) -> a1 * a2,
        Neg(a) -> -a,
    }
}
fn extract_value2(a: Expr<i32>) -> i32 {
    match a {
        Const(v) -> v - 1,
        Add(a1, a2) -> a1 ^ a2,
        Mul(a1, a2) -> a1 - a2,
        Neg(a) -> a + 3,
    }
}
fn extract_value3(a: Expr<String>) -> String {
    match a {
        Const(v) -> to_string(v),
        Add(a1, a2) -> a1 + a2, // string concatenation
        Mul(a1, a2) -> a1 + ", but at the same time, " + a2,
        Neg(a) -> "negation of " + a,
    }
}
```

In a sense, packaged inside each one of these arrows are essentially 4 functions, in the following sense: suppose someone gives me an arrow `fn extract_value(a: Expr<A>) -> A` for some `A`. I can always extract the following four functions:

```rust
fn extract_Const(a: i32) -> A     { extract_value(Const(a)) }
fn extract_Add(a1: A, a2: A) -> A { extract_value(Add(a1, a2)) }
fn extract_Mul(a1: A, a2: A) -> A { extract_value(Mul(a1, a2)) }
fn extract_Neg(a: A) -> A         { extract_value(Neg(a)) }
```

Instead, pick expr like this:

```rust
enum SimpleExpr<A> {
    Nothing(Unit),
    Mul(A, A),
}
```

A function with this signature `fn extract_value(a: SimpleExpr<A>) -> A` essentially corresponds to giving these two functions:

```rust
fn extract_Nothing() -> A { extract_value(Nothing) }
fn extract_Nothing(w: Unit) -> A { extract_value(Nothing) }
fn extract_Mul(a1: A, a2: A) -> A { extract_value(Mul(a1, a2)) }
```

What is the purpose of a function that takes something of type unit and just gives me `A`? It's the same as a concrete value of `A`!

```rust
fn extract_value(a: SimpleExpr<i32>) -> i32 {
    match a {
        Nothing(u: Unit) => 57,
        Mul(a: A, b: B) => a + b,
    }
}
```

Functions from unit into $A$ are the same as just values of $A$. (in Prog.)

# Products and coproducts and functors

## Exercise $A \times -$

Take any category $C$ with all products.

# Algebraic data types:

One can use `Either` and `Pair` and `Unit` and `Empty`, i.e., products and coproducts, to construct a lot of algebraic data types in any category $C$: however, only non-recursive ones! We do not know yet how to capture recursive, that's what we will see in this lecture.

```rust
enum Either<A,B> {
    Left(A),
    Right(B),
}
// the coproduct of Int and String
enum EitherIntString {
    Left(i32),
    Right(String),
}
// the product of Int and String
struct PairIntString {
    first: i32,
    second: String
}


// abstractly: this is the functor that sends X to 1 + X.
enum Maybe<X> {
    Nothing,
    Just(X),
}

// another example:
enum MaybeWithInt<X> {
    No,
    JustTwice(X, i32)
}
// these two types are isomorphic:
enum MaybeWithInt<X> {
    No(Unit),
    JustTwice(Pair<X, i32>)
}
// abstractly: this is the functor that sends X to 1 + (X x i32)
enum MaybeWithInt<X> {
    No,
    JustTwice(X, i32)
}
type MaybeWithInt<X> = Either<Unit, Pair<X, i32>>

// an example where it's a struct instead of an enum

// abstractly: this is the functor that sends X to String x i32 x X x X.
struct Person<X> {
    name: String,
    age: i32,
    data: X,
    backup: X
}
struct Person<X> {
    name: String,
    age: i32,
    dataAndBackup: Pair<X,X>,
}
struct Person<X> {
    name: String,
    ageAnddataAndBackup: Pair<i32,Pair<X,X>>,
}
struct Person<X> {
    nameAndageAnddataAndBackup: Pair<String,Pair<i32,Pair<X,X>>>,
}
type Person<X> = Pair<String,Pair<i32,Pair<X,X>>>.

// idea: I can capture non-recursive datatypes as long as I have coproducts, products, terminals, initials.

// exercise
enum F<X> {
    Empty,
    Constr(i32, X)
}
// the point is to convert this datatype to an equivalent one that only uses Either, Pair, Unit, Empty.
type F<X> = Either<Unit, Pair<i32, X>>

enum Expr<X> {
    Const(i32),
    Add(X, X),
    Mul(X, X),
    Neg(X),
}

type Expr<X> = Either<i32, Either<Pair<X,X>, Either<Pair<X,X>, X>>>
```

## Claim

```rust
enum Maybe<X> {
    Nothing,
    Just(X),
}
```

## Claim 1. There is an endofunctor on $\text{Prog}$ whose program on arrows send `X` to `Maybe<X>`.

2. *(Program on arrows.)* If someone comes up to me gives me an arrow `fn f(a: A) -> B`, I can construct an arrow

```rust
fn Maybe_arrows_f(a: Maybe<A>) -> Maybe<B> {
    match a {
        Nothing => Nothing,
        Just(a:A) => Just(f(a)),
    }
}
```

3. *(The functor sends identities to identities)*
I have to check
```rust
Maybe_arrows_id = id[Maybe<A>]
```

```rust
fn Maybe_arrows_id(a: Maybe<A>) -> Maybe<A> {
    match a {
        Nothing => Nothing,
        Just(a:A) => Just(id(a)),
    }
}
```

This is program equivalent to

```rust
fn Maybe_arrows_id(a: Maybe<A>) -> Maybe<A> {
    a
}
```

## Claim 2. `Maybe<X>` is isomorphic to `Either<Unit, X>`.

```rust
enum Maybe<X> {
    Nothing,
    Just(X),
}
```

This type is isomorphic to this one

```rust
type Maybe2<X> = Either<Unit, X>

enum Either<A,B> {
    Left(A)
    Right(B),
}

enum Maybe2<X> {
    Left,
    Right(X),
}
```

## Claim 3.

For any category $C$ with coproducts and a terminal object $1$, there is an endofunctor $F$ defined by taking an object $X$ and sending it to $1 + X$.

- I need to show that if I have an arrow $f : A \to B$, then I have an arrow $F(f) : 1 + A \to 1 + B$.

This is an abstract way of talking about `Maybe` but in any category $C$ that has a little bit of structure.
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>
<br>


## Definition: algebra for an endofunctor $F$

Intuition: an "algebra" is a simply a packet of arrows with certain domain and codomains, precisely in the way described above in the case of $\text{Prog}$.

Take any category $C$, and an endofunctor $F : C \Rightarrow C$.

*Definition.* An *algebra for $F$* is composed of the following choices:
1. A choice of an object $X$ of $C$.
2. A choice of an arrow $\alpha : F(X) \to X$.

Given two algebras for the same functor $F$, I define an "arrow between algebras" as the following choices:

*Definition.* An *algebra arrow* from the algebra $(X,\alpha)$ to the algebra $(Y,\beta)$ is defined as the following choices:
1. A choice of an arrow $f : X \to Y$,
2. Such that the equation $\alpha \,; f = F(f) \,; \beta$ holds, i.e., the following square commutes:
$$
\begin{array}{ccccccccc}
\phantom{\alpha} F(X) & \xrightarrow{F(f)} & F(Y) \phantom{\beta} & \\
{\alpha} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {\beta}\\
\phantom{\alpha} X & \xrightarrow{\phantom{iii}f\phantom{iii}} & Y \phantom{\beta} \\
\end{array}
$$

Of course, the things above assemble in a category!

## Definition: the category of algebras $\textsf{Alg}(F)$

1. *(Objects).* The type of objects has a value for each possible algebra for $F$.
2. *(Arrows).* The type of arrows from one algebra $A$ to another one $A'$ has a value of each possible *algebra arrow* from $A$ to $A'$.
3. *(Identities).* There is an identity algebra arrow, as follows:
$$
\begin{array}{ccccccccc}
\phantom{\alpha} F(X) & \xrightarrow{F(\textsf{id}_X)} & F(X) \phantom{\alpha} & \\
{\alpha} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {\alpha}\\
\phantom{\alpha} X & \xrightarrow{\phantom{iii}\textsf{id}\phantom{iii}} & X \phantom{\alpha} \\
\end{array}
$$
We need to check that this is indeed an algebra arrow, i.e., that $\alpha \,; \textsf{id}_X = F(\textsf{id}_X) \,; \alpha$: this works because $\alpha \,; \textsf{id}_X = \alpha = \textsf{id}_{F(X)} \,; \alpha = F(\textsf{id}_X) \,; \alpha$.

## Definition: coalgebra for an endofunctor $F$

Take any category $C$, and an endofunctor $F : C \Rightarrow C$.

A coalgebra for $F$ is just an algebra in $C^\textsf{op}$. Explicitly, a coalgebra for $F$ is composed of the following choices:
1. A choice of an object $X$ of $C$.
2. A choice of an arrow $\alpha : X \to F(X)$.

### Example:

Take this functor here:

```rust
struct Aut<A> {
    f: bool,
    t: List<Pair<Char,A>>
}
```

This is a functor: assume `fn f(a: A) -> B`,

```rust
fn Aut_arrows_f(a: Aut<A>) -> Aut<B> {
    Aut {
        f: a.f,
        t: List.map(a.t, |x:Pair<Char,A>| Pair { first: x.first, second: f(x.second) })
    }
}
```

What is a coalgebra for `F`? It is a choice of a type `A` along with a choice of a program `fn produce(a: A) -> Aut<A>`. This last choice essentially amounts to these choices:

```rust
fn produce_f(a: A) -> bool { produce(a).f }
fn produce_t(a: A) -> List<Pair<Char,A>> { produce(a).t }
```

Example:
I want to give an coalgebra for this functor Aut.

1. I have to pick an object of Prog, so I pick this type here:
```rust
enum State {
    A, B, C, D
}
```
2. I have to pick the other arrow of the choice of a coalgebra for Aut, which is:
```rust
fn produce_f(a: State) -> Aut<State> {
    match a {
        A => Aut { f: false, t: [('a', C), ('b', D), ('c', A)] }
        B => Aut { f: false, t: [('a', A), ('c', B)] }
        C => Aut { f: true,  t: [('b', C), ('a', D)] }
        D => Aut { f: false, t: [('z', B)] }
    }
}
```


----

Other variations on the previous:

```rust
// Functor whose coalgebras are deterministic automata
struct DetAut<A> {
    f: bool,
    t: Func<Char, A>
}
// Functor whose coalgebras are non-deterministic automata, now using a delta function
struct NonDetAut<A> {
    f: bool,
    t: Func<Char, List<A>>
}
// Functor whose coalgebras are deterministic automata whose delta function can fail
struct DetAutWithFail<A> {
    f: bool,
    t: Func<Char, Maybe<A>>
}
```

A coalgebra `fn produce(a: A) -> DetAut<A>` essentially gives you:

```rust
fn produce_f(a: A) -> bool { produce(a).f }
fn produce_t(a: A) -> Func<Char, A> { produce(a).t } // by currying, it's just...
fn produce_t(a: A, c: Char) -> A { produce(a).t(c) }
```

A coalgebra `fn produce(a: A) -> NonDetAut<A>` essentially gives you:

```rust
fn produce_f(a: A) -> bool { produce(a).f }
fn produce_t(a: A) -> Func<Char, List<A>> { produce(a).t } // by currying, it's just...
fn produce_t(a: A, c: Char) -> List<A> { produce(a).t(c) }
```

A coalgebra `fn produce(a: A) -> DetAutWithFail<A>` essentially gives you:

```rust
fn produce_f(a: A) -> bool { produce(a).f }
fn produce_t(a: A) -> Func<Char, Maybe<A>> { produce(a).t } // by currying, it's just...
fn produce_t(a: A, c: Char) -> Maybe<A> { produce(a).t(c) }
```

# Main example: `ListInt` as initial algebra.


The recursive data type that we will look at now:
```rust
enum ListInt {
    Empty,
    Element(i32, ListInt)
}
```

> RECALL:
>
> In any category $C$, being initial is a universal property: if you give me two objects $I,I'$ which are both initial, then they are isomorphic.

*(Definition.)* An *initial algebra for $F$* is simply an initial object in $\textsf{Alg}(F)$.

Take this endofunctor of $\text{Prog}$:

```rust
enum ListIntAlg<X> {
    AEmpty,
    AElement(i32, X)
}

type ListIntAlg<X> = Either<Unit, Pair<i32, X>>
// ListIntAlg(X) := 1 + i32 × X

// as usual, assume fn f(a: A) -> B.
// crucial: this function is not recursive! So it can be expressed in the term language.
fn FL_arrows_f(a: ListIntAlg<A>) -> ListIntAlg<B> {
    match a {
        AEmpty => AEmpty,
        AElement(v: i32, x: A) -> AElement(v, f(a))
    }
}
```

What does an algebra for this functor essentially provide?

> RECALL:
>
> There is a fold map, parametric for every `A`:
>
> The idea:

$$[7,3,8,2]$$

1. How do I compute the sum of this list?

You pick a operation/program $a \otimes b$, and you pick a value $z$.

The fold function does essentially this:

$$7 \otimes (3 \otimes (8 \otimes (2  \otimes z))).$$

In this case, the $ \otimes $ program must be able to take an integer as one of the arguments, and then it can return whatever it wants.

If I pick $\otimes$ to do the sum of its arguments, and I pick $z = 0$, then I obtain the sum.

```rust
fn otimes(a: i32, b: i32) -> i32 {
    a + b
}
```

$$7 + (3 + (8 + (2  + 0))).$$

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

>
> ```rust
> fn ListInt_foldr<A>(base: A, func: Func<Pair<i32, A>, A>, list: ListInt) -> A {
>     match list {
>         AEmpty => base,
>         AElement(v: i32, tail: ListInt) -> func(v, ListInt_foldr(base, func, tail))
>     }
> }
> ```
> Alternatively, one can say:
>
> If you give me these functions,
> 1. `fn base(_: Unit) -> A`, which is the same as just saying an element of `A`, just like `base: A`,
> 2. `fn func(v: i32, a: A) -> A`,
> then I can cook up a function
>
> ```rust
> fn ListInt_foldr[base][func](list: ListInt) -> A {
>     match list {
>         AEmpty => base,
>         AElement(v, tail: ListInt) -> func(v, ListInt_foldr[base][func](tail))
>     }
> }
> ```

We show, formally, that there is an initial algebra for this `ListInt` is
