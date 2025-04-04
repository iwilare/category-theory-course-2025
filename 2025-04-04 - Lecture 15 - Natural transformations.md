# 2025-04-03 - Lecture 15 (Natural transformations)

Very generic idea:

- Functors are arrows between categories.
- Natural transformations are arrows between functors.
- Naturality captures parametricity of functions with respect to a family of types.

## Parametricity in programming languages

In most programming languages you know and love (with, until recently, the notable exception of Go) you can define functions in terms of a type parameter, which we indicate here with `<A>` and with `<A,B>` when you have multiple ones.

```rust
fn identity<A>(a: A) -> A {
    a
}

fn fst<A, B>(x: Pair<A, B>) -> A {
    match p {
        Pair(a, b) -> a
    }
}

fn duplicate<A>(a: A) -> Pair<A, A> {
    Pair(a, a)
}

fn get_right<A, B>(e: Either<A, B>) -> Maybe<B> {
    match e {
        Left(_) => None,
        Right(b) => Some(b),
    }
}

fn head<A>(l: List<A>) -> Maybe<A> {
    match l {
        Empty => None,
        Element(a, _) => Some(a),
    }
}

fn tail<A>(l: List<A>) -> List<A> {
    match l {
        Empty => Empty,
        Element(_, tail) => tail,
    }
}

fn empty<A>(x: Unit) -> List<A> {
    Empty
}

fn oneElement<A>(x: A) -> List<A> {
    Element(x, Empty)
}

fn length<A>(l: List<A>) -> i32 {
    match l {
        Empty => 0,
        Element(_, tail) => 1 + length(tail),
    }
}

fn append<A>(p: Pair<List<A>, List<A>>) -> List<A> { ... }
fn append<A>(l1: List<A>, l2: List<A>) -> List<A> {
    match l1 {
        Empty => l2,
        Element(a, tail) => Element(a, append(tail, l2)),
    }
}

fn add_end<A>(l: List<A>, v: A) -> List<A> {
    match l {
        Empty => oneElement(v),
        Element(a, tail) => Element(a, add_end(tail, v)),
    }
}

fn add_end_pair<A>(p: Pair<List<A>, A>) -> List<A> {
    match p {
        Pair(Empty, v) => oneElement(v),
        Pair(Element(a, tail), v) =>
            Element(a, add_end_pair(Pair(tail, v))),
    }
}

// join([[4,2,1],[20],[7,3]]) = [4,2,1,20,7,3]
fn join<A>(lists: List<List<A>>) -> List<A> {
    match lists {
        Empty => Empty,
        Element(list, tail) => append(list, join(tail)),
    }
}

fn reverse<A>(l: List<A>) -> List<A> {
    match l {
        Empty => Empty,
        Element(a, tail) => add_end(a, reverse(tail)),
    }
}

// This is an endofunctor, the initial algebra for this is exactly BinaryTree<i32>.
enum BinaryTreeIntAlg<X> {
    Leaf(i32),
    Branch(X, i32, X)
}

/*
// Functors from product categories:
enum BinaryTreeIntAlg<A,X> {
    Leaf(A),
    Branch(X, A, X)
}
enum Either<A,B> {
    Left(A),
    Right(B),
}
*/

enum BinaryTree<A> {
    Leaf(A),
    Branch(BinaryTree<A>, A, BinaryTree<A>)
}

fn flattenInOrder(t: BinaryTree<A>) -> List<A> {
    match t {
        Leaf(a) => oneElement(a),
        Branch(l, a, r) => {
            let listL = flattenInOrder(l);
            let listR = flattenInOrder(r);
            append(listL, Element(a, listR))
        }
    }
}
```

Crucial intuition: natural transformations capture precisely this idea of a family of arrows which is parametric on every type (i.e., in Prog, these are just the objects of C!)

In most cases we will consider natural transformations between endofunctors, since we care about Prog.

## Definition: Natural transformation between $F : C \Rightarrow D$ and $G : C \Rightarrow D$

For any two categories $C,D$ and any two functors, $F,G : C \Rightarrow D$ (they need to have the same signature!),
a **natural transformation from $F$ to $G$** is given by the following choices:
1. *(Family of arrows.)* For every object $X$ of $C$, a choice of an arrow $\alpha_X$ (a different arrow $\alpha_X,\alpha_Y,...$ for each possible object of $C$) with this source and target:
$$\alpha_X : F(X) \longrightarrow G(X)$$
2. *(Naturality.)* The choice made above must be such that the following square commutes for every possible choice of $X,Y$ objects of $C$ and arrows $f : X \to Y$ of $C$ (this is called the naturality square):

    $$
    \begin{array}{ccccccccc}
    \phantom{F(f)} F(X) & \xrightarrow{\phantom{iii}\alpha_X\phantom{iii}} & G(X) \phantom{G(f)} & \\
    {F(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {G(f)}\\
    \phantom{F(f)} F(Y) & \xrightarrow{\phantom{iii}\alpha_Y \phantom{iii}} & G(Y) \phantom{G(f)} \\
    \end{array}
    $$

    i.e., equationally, $$\alpha_X \,; G(f) = F(f) \,; \alpha_Y$$ for every possible $f : X \to Y$.

**Insert graphical intuition here, using functors as shadows.**

# Natural transformations in Rust

The parametric functions that we have seen before in Rust are precisely natural transformations between certain endofunctors of Rust.

## Naturality of `head`

> `head` is a natural transformation from the `List` endofunctor to the `Maybe` endofunctor.

```rust
fn head<A>(l: List<A>) -> Maybe<A> {
    match l {
        Empty => None,
        Element(a, _) => Some(a),
    }
}
```

*Disclaimer.* The `<A>` part does not exist categorically... here is what's going on more precisely.
- The "abstract" definition of natural transformation  tells me to define, for each possible object `X` of the source category an arrow of the target category. (here source category and target category of the functors involved are the same since they are all endofunctors).
- I can do this definition in a single fell swoop by defining a single function in Rust *because* of the `<A>` notation. But in theory, nothing theoretically stops me to define a family of *separate* functions, one for each specific type, like this:

```rust
fn head_i32(l: List<i32>) -> Maybe<i32> {
    match l {
        Empty => None,
        Element(a, _) => Some(a),
    }
}
fn head_String(l: List<String>) -> Maybe<String> {
    None
}
fn head_ListInt(l: List<ListInt>) -> Maybe<ListInt> {
    match l {
        Empty => None,
        Element(a, _) => Some(a),
    }
}
```

The only problem is that this would take an infinite amount of time, so instead Rust gives me a convenient notation. It is more clear now that, because I'm secretly doing a lot of choices, all of these choices need to be "compatible" with one another.

---
---

*The intution of "all of these choices must have exactly the same code" is precisely captured by the naturality condition.*

> *Intuition*: mapping a function `f` over a list, and then taking the first element, is the same as taking the first element and then mapping the function `f` inside of the Maybe.

```
Start from ["32","43","1"]
1. Take the head:
   "32"
2. Convert it to integer
   32
             or

1. Convert everything in the list to integer
   [32,43,1]
2. Take the head:
   32
```

*Equationally in Rust* the following two functions are equivalent programs for every type `X` and every function `fn f(a: X) -> Y`:

```
compose_(List_arrows_f)_head = compose_head_(Maybe_arrows_f)
```

By program equivalence, this means that for every value `xs: List<X>`:

```
xs.List_arrows_f().head() = xs.head().Maybe_arrows_f()
```

*Proof.*

- Case `xs = Empty`:

  ```
    Empty.List_arrows_f().head()
  = Empty.head()
  = None
  ```
  ```
    Empty.head().Maybe_arrows_f()
  = None.Maybe_arrows_f()
  = None
  ```
- Case `xs = Element(a, as)`:

  ```
    Element(a, as).List_arrows_f().head()
  = Element(f(a), List_arrows_f(as)).head()
  = Some(f(a))
  ```
  ```
    Element(a, as).head().Maybe_arrows_f()
  = Some(a).Maybe_arrows_f()
  = Some(f(a))
  ```

## Naturality of `tail`

*Exercise!*

## Naturality of `length`

```rust
fn length<A>(l: List<A>) -> i32 {
    match l {
        Empty => 0,
        Element(_, tail) => 1 + length(tail),
    }
}
```

*What are the two functors at play here?*
The type `A` does not appear in the expression of the return type.

*Recall* that a constant functors on a type $K$ of the category $D$ is given by mapping objects in $C$ into $K$ of the category $D$, and by mapping every morphism into the identity on $K$.

> `length` is a natural transformation from the `List` functor to the constant functor at `i32`.

*Equationally in Rust*: by program equivalence, the naturality condition this means that for every type `X` and object `xs: List<X>`:

```
xs.List_arrows_f().length() = xs.length().ConstInt_arrows_f()
                            = xs.length().id()
                            = xs.length()
```

> *Intuition*: for any list, mapping a function over a list does not change its length.

*Proof.*

- Case `xs = Empty`:

  ```
    Empty.List_arrows_f().length()
  = Empty.length()
  = 0
  ```
  ```
    Empty.length().ConstInt_arrows_f()
  = Empty.length()
  = 0
  ```
- Case `xs = Element(a, as)`:

  ```
    Element(a, as).List_arrows_f().length()
  = Element(f(a), as.List_arrows_f()).length()
  = 1 + as.List_arrows_f()
  ```
  ```
    Element(a, as).length().ConstInt_arrows_f()
  = 1 + length(as)
  ```

  By inductive hypothesis, `as.List_arrows_f().length() = length(as)`.
  Therefore, the two expressions are equal.

## Naturality of `add_end`

*Exercise!*

> ***Note***: categorically, `add_end` can be seen as just a shorthand for `add_end_pair`.

> `add_end_pair` is a natural transformation from the functor (`A` $\mapsto$ `Pair<List<A>, A>`) to the `List` functor.

You can check that (`A` $\mapsto$ `Pair<List<A>, A>`) and doing the intuitive thing on morphisms is indeed a functor.

Naturality says that for any pair `p: Pair<List<A>, A> = Pair(xs, a): `,
```
Pair(xs, a).add_end_pair().List_arrows_f() = Pair(xs, a).PairListA_arrows_f.add_end_pair()
```
Or, in other words,
```
Pair(xs, a).add_end_pair().List_arrows_f() = Pair(xs.List_arrows_f(), f(a)).add_end_pair()
```

## Naturality of `reverse`

> `reverse` is a natural transformation from the `List` functor to the `List` functor.

```
xs.List_arrows_f().reverse() = xs.reverse().List_arrows_f()
```

> *Intuition*: mapping a function and then reversing is the same as reversing and then mapping.
> ```
>     [   a,    b,    c,    d,    e] <mapping>
>     [f(a), f(b), f(c), f(d), f(e)] <reverse>
>     [f(e), f(d), f(c), f(b), f(a)]
> ```
> ```
>     [   a,    b,    c,    d,    e] <reverse>
>     [   e,    d,    c,    b,    a] <mapping>
>     [f(e), f(d), f(c), f(b), f(a)]
> ```

*Proof.*

- Case `xs = Empty`:

  ```rust
    Empty.List_arrows_f().reverse()
  = Empty.reverse()
  = Empty
  ```
  ```rust
    Empty.reverse().List_arrows_f()
  = Empty.List_arrows_f()
  = Empty
  ```
- Case `xs = Element(a, as)`:

  ```rust
    Element(a, as).List_arrows_f().reverse()
  = Element(f(a), as.List_arrows_f()).reverse()
  = (as.List_arrows_f().reverse()).add_end(f(a))
  = as.List_arrows_f().reverse().add_end(f(a))
  ```

  ```rust
    Element(a, as).reverse().List_arrows_f()
  = as.reverse().add_end(a).List_arrows_f() // Naturality of add_end<A>
  = as.reverse().List_arrows_f().add_end(f(a))
  ```

  By inductive hypothesis,

  ```rust
    as.List_arrows_f().reverse() = as.reverse().List_arrows_f()
  ```

  which makes the two expressions equal.

---

## Naturality of `getRight`

> For any `K`, `getRight<K,A>` is a natural transformation between `Either<K,_>` and `Maybe<_>`.

For any type `A`, and value `e: Either<K,A>`:

```
e.EitherK_arrows_f().getRight() = e.getRight().Maybe_arrows_f()
```

*Proof.*

- Case `e = Left(k)`:

  ```
    Left(k).EitherK_arrows_f().getRight()
  = Left(k).getRight()
  = None
  ```
  ```
    Left(k).getRight().Maybe_arrows_f()
  = None.Maybe_arrows_f()
  = None
  ```
- Case `e = Right(a)`:

  ```
    Right(a).EitherK_arrows_f().getRight()
  = Right(f(a)).getRight()
  = Some(f(a))
  ```
  ```
    Right(a).getRight().Maybe_arrows_f()
  = Some(a).Maybe_arrows_f()
  = Some(f(a))
  ```

## Naturality of `duplicate`

*What are the two functors at play here?*

```rust
fn<A> duplicate(a: A): Pair<A,A> => Pair(a, a)
```

The second functor sends each type `A` to the type `Pair<A,A>`, we checked last time that this is indeed a functor.

*Recall* the identity functor $id_C : C \to C$: on objects, it assigns to each type $K$ of the category $D$ the same type $K$. On morphisms it acts as the identity function, sending `f` to `f`.

> `duplicate` is a natural transformation between the identity functor `_` and `Pair<_,_>`.

(We refer explicitly to `Identity` and `DoublePair` as the two functors.)

*Equationally in Rust*:

For any type `A`, and value `a: A`:

```
a.Identity_arrows_f().duplicate() = a.duplicate().DoublePair_arrows_f()
```

By unfolding,

```
a.f().duplicate() = a.duplicate().DoublePair_arrows_f()
```

*Proof.*

```
  a.f().duplicate()
= Pair(f(a), f(a))
```
```
  a.duplicate().DoublePair_arrows_f()
= Pair(a, a).DoublePair_arrows_f()
= Pair(f(a), f(a))
```

# Naturality and parametricity

*(Claim.)* If a language is capable of defining different functions for different types, then the family that you get is not going to be natural in general.

---

*(Claim.)* Every parametric function definable in a programming language which *CANNOT* distinguish on the specific type `X` that you're defining the function on, defines a true natural transformation (as long as you can talk about the functors involved and you have checked that these are indeed functors.)

Java is not one of these languages; I can use Java generics, but actually it is possible to inspect what type I'm defining the function for.

A funny reference connecting parametricity and naturality: *Theorems for free! (by Philip Wadler)*

# Working with natural transformations

### The identity natural transformation

For any functor $F : C \Rightarrow D$, there is always a natural transformation $\alpha : F \longrightarrow F$ for any functor

$$
    \begin{array}{ccccccccc}
    \phantom{F(f)} F(X) & \xrightarrow{\phantom{iii}\textsf{id}_X\phantom{iii}} & F(X) \phantom{F(f)} & \\
    {F(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {F(f)}\\
    \phantom{F(f)} F(Y) & \xrightarrow{\phantom{iii}\textsf{id}_Y \phantom{iii}} & F(Y) \phantom{F(f)} \\
    \end{array}
$$

### Composition of natural transformations (also called "vertical composition")

Take three functors $F,G,H : C \Rightarrow D$, all from the same category to the same category.

Imagine we have a natural transformation $\alpha : F \longrightarrow G$ and a natural transformation $\beta : G \longrightarrow H$.

Then, I can cook you up a new natural transformation $\gamma : F \longrightarrow H$, which I write like as $\alpha \,; \beta$ (note: this is NOT a composition of any category yet. What I wrote does not even make sense! It's just notation.)

The family $\gamma$ is defined like this:
$$\gamma_X := \alpha_X \,; \beta_X$$

$$
\begin{array}{ccccccccccc}
\phantom{F(f)} F(X) & \xrightarrow{\phantom{ii}\alpha_X\phantom{ii}} & G(X) & \xrightarrow{\phantom{ii}\beta_X\phantom{ii}} & H(X) \phantom{F(f)} \\
{F(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {G(f)} & \raisebox{2pt}{\tiny =} & \downarrow {H(f)} \\
\phantom{F(f)} F(Y) & \xrightarrow{\phantom{ii}\alpha_Y\phantom{ii}} & G(Y) & \xrightarrow{\phantom{ii}\beta_Y\phantom{ii}} & H(Y) \phantom{F(f)} \\
\end{array}
$$

$$
\begin{array}{ccccccc}
\phantom{F(f)} F(X) & \xrightarrow{\phantom{ii} \alpha_X \,;\, \beta_X \phantom{ii}} & H(X) \phantom{F(f)} \\
{F(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {H(f)} \\
\phantom{F(f)} F(Y) & \xrightarrow{\phantom{ii} \alpha_Y \,;\, \beta_Y \phantom{ii}} & H(Y) \phantom{F(f)} \\
\end{array}
$$

**Graphical intuition for why it's called vertical intuition.**

### Composition of natural transformations (also called "horizontal composition")

Remember: we know how to compose functors together.

Now, imagine this situation: three categories,
- $C$,
- $D$,
- $E$,

Four functors:
- $F, F' : C \Rightarrow D$
- $G, G' : D \Rightarrow E$

Two natural transformations:
- $\alpha : F \longrightarrow F'$
- $\beta : G \longrightarrow F'$

Then, I cook up a new natural transformation like this

- $\gamma : (F\,;G) \longrightarrow (F'\,; G')$

which we will denote as $\gamma := \alpha * \beta$.

$$
\begin{array}{rcl}
(\beta \ast \alpha)_X &:=& G(\alpha_X) \,; \beta_{F'(X)}  \\
\end{array}
$$

We need to check that this family of arrows is indeed a natural transformation.
That is, we need to show that for every object $X,Y$ in $C$ and any arrow $f : X \to Y$, the following square commutes:

$$
\begin{array}{ccccccc}
\phantom{F(f)} G(F(X)) & \xrightarrow{\phantom{ii} G(\alpha_X) \phantom{ii}} & G(F'(X)) & \xrightarrow{\phantom{ii} \beta_{F'(X)} \phantom{ii}} & G'(F'(X)) \phantom{F(f)} \\
G(F(f)) \downarrow & \raisebox{2pt}{\tiny =} & \downarrow G(F'(f)) & \raisebox{2pt}{\tiny =} & \downarrow G'(F'(f)) \\
\phantom{F(f)} G(F(Y)) & \xrightarrow{\phantom{ii} G(\alpha_Y) \phantom{ii}} & G(F'(Y)) & \xrightarrow{\phantom{ii} \beta_{F'(Y)} \phantom{ii}} & G'(F'(Y)) \phantom{F(f)} \\
\end{array}
$$

$$
\begin{array}{ccc}
\phantom{F(f)} G(F(X)) & \xrightarrow{\phantom{ii} (\beta \ast \alpha)_X \phantom{ii}} & G'(F'(X)) \phantom{F(f)} \\
G(F(f)) \downarrow & \raisebox{2pt}{\tiny =} & \downarrow G'(F'(f)) \\
\phantom{F(f)} G(F(Y)) & \xrightarrow{\phantom{ii} (\beta \ast \alpha)_Y \phantom{ii}} & G'(F'(Y)) \phantom{F(f)} \\
\end{array}
$$

**Graphical intuition for why it's called horizontal intuition.**

# The functor category

...

# Special functors: the arrows-from-$Γ$ functor

Take a category $C$.

Take a specific object $\Gamma$ of $C$, any object whatsoever works.

I cook up a functor $\textsf{into}_\Gamma : C \to \text{Prog}$.

- *(Program on objects.)* Given an object $X$ of $C$, I send it to the object of $\text{Prog}$ type `Arr_Γ_X`.
- *(Program on arrows.)* Given an arrow $f : X \to Y$ for some $X,Y$ objects of $C$, I need to construct and arrow in `Prog` with this signature here:

    ```rust
    fn IntoΓ_arrows_f(p: Arr_Γ_X) -> Arr_Γ_Y {
        compose(p, f)
    }
    ```
- *(Respects identities)*
    ```rust
    fn IntoΓ_arrows_f(p: Arr_Γ_X) -> Arr_Γ_X {
        compose(p, id)
    }
    // this is equivalent to... the identity arrow on Prog!
    // so when f=identity I send it to the identity on Prog.
    fn IntoΓ_arrows_f(p: Arr_Γ_X) -> Arr_Γ_X {
        p
    }
    ```
- *(Respects compositions)* Same story.
