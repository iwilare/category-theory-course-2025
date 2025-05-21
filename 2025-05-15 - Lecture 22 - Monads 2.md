# 2025-05-15 - Lecture 22 (Monads 2)

# Extension form of monads

There is an equivalent *presentation* of monads.

Take an endofunctor $M : C \Rightarrow C$. (actually you don't even need the functor map, just a function on objects.)

An *extension system* for $M$ consists of the following choices:

1. For every object $A$ of $C$, an arrow $\eta_A : A \to M(A)$,
2. For every object $A,B$ of $C$, given an arrow $$f : A \to M(B),$$ a choice of an arrow $$\overline f : M(A) \to M(B).$$
3. For every arrow $f : A \to M(B)$, $$\eta_A \,; \overline{f} = f$$
4. For every arrow $f : A \to M(B)$, $g : B \to M(C)$, $$\overline{f} \,; \overline{g} = \overline{(f \,; \overline{g})}$$

# These two presentations are equivalent

*(Theorem.)* Giving an extension system for an endofunctor $M : C \Rightarrow C$ is
   equivalent to giving a monad structure on $M : C \Rightarrow C$.

*Proof.*

*(Monad to extension system.*)

Assume that I am given a monad $M$
- I need to give an arrow $\eta_A : A \to M(A)$, but these are exactly the components $\textsf{unit}_A$ on $A$ of $\textsf{unit}$.
- Someone gives me an arrow $f : A \to M(B)$, I need to give an arrow $\overline f : M(A) \to M(B)$.

$$
M(A) \xrightarrow{M(f)} M(M(B)) \xrightarrow{\textsf{mul}_B} M(B)
$$

so I define $\overline f := M(f) \,; \textsf{mul}_B$.

Let's check the first property of an extension system.

$$
\begin{array}{lll}
  & \eta_A \,; \overline{f} & \text{(definition)} \\
= & \textsf{unit}_A \,; M(f) \,; \textsf{mul}_B & \text{(naturality of \textsf{unit})}\\
= & f \,; \textsf{unit}_{M(B)} \,; \textsf{mul}_B & \text{(monad identity law)}\\
= & f
\end{array}
$$

Let's check the third property of an extension system.

For every arrow $f : A \to M(B)$, $g : B \to M(C)$, $$\overline{f} \,; \overline{g} = \overline{(f \,; \overline{g})}$$

$$
\begin{array}{lll}
  & \overline{f} \,; \overline{g}  & \text{(definition)} \\
= & M(f) \,; \textsf{mul}_B \,; M(g) \,; \textsf{mul}_C & \text{(naturality of \textsf{mul})} \\
= & M(f) \,; M(M(g)) \,; \textsf{mul}_{M(C)} \,; \textsf{mul}_C & \text{(monad associativity)}  \\
= & M(f) \,; M(M(g)) \,; M(\textsf{mul}_C) \,; \textsf{mul}_C & \text{(bottom-to-top: functoriality)}   \\
= & M(f \,;  M(g) \,; \textsf{mul}_C) \,; \textsf{mul}_C & \text{(bottom-to-top: def. of extension)}  \\
= & \overline{f \,;  M(g) \,; \textsf{mul}_C} &  \text{(bottom-to-top: def. of extension)} \\
= & \overline{(f \,; \overline{g})} &
\end{array}
$$

Remember that $\textsf{mul}$ must be a natural transformation: so, for every $X,Y$ and $f : X \to Y$,
  $$
    \begin{array}{ccccccccc}
    \phantom{M(M(f))} M(M(X)) & \xrightarrow{\phantom{iii}\textsf{mul}_X\phantom{iii}} & M(X) \phantom{M(f)} & \\
    {M(M(f))} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {M(f)}\\
    \phantom{M(M(f))} M(M(Y)) & \xrightarrow{\phantom{iii}\textsf{mul}_Y \phantom{iii}} & M(Y) \phantom{M(f)} \\
    \end{array}
  $$

*(Extension system to monad.*)

- I need to construct a natural transformation $\textsf{unit}_X : X \to M(X)$. The family of arrow is given to me by the first point of an extension system, so not much to do. I should check that this really is a natural transformation, i.e, that the square commutes.
- I need to construct a natural transformation $\textsf{mul}_X : M(M(X)) \to M(X)$. The family of arrows is given to me by this choice:

> 1. For every object $A,B$ of $C$, given an arrow $$f : A \to M(B),$$ a choice of an arrow $$\overline f : M(A) \to M(B).$$

I choose $A := M(X)$, $B := X$, I pick $f := \textsf{id}_X$, and in return I get an arrow $\overline {\textsf{id}_X} : M(M(X)) \to M(X)$. This is exactly how I define the component on $X$ of my natural transformation $\textsf{mul}_X$.

I should also check the monad laws: these follow by point 3. and 4. of the definition of extension system.

- ...

# Kleisli category to monads

Someone gives me an endofunctor $M : C \Rightarrow C$.

The Kleisli category is defined by having arrows from $X$ to $Y$ to be arrows $X \to M(Y)$.

*(Claim.*) Then, if the Kleisli category really is a category, i.e., it comes equipped with identities and a composition program, then $M$ comes equipped with monad structure. There is a monad structure for each possible way in which you can define composition.

*Proof.*

1. The fact that the category *has* identities, means that it comes equipped with a family of arrows $\textsf{id}_A : A \to M(A)$.

2. Imagine someone comes up to me and tells me that I can compose Kleisli arrows together:

Given $f : X \to M(Y)$, $g : Y \to M(Z)$ I get $f\,; g : X \to M(Z)$ for free.

Then, I also get a natural transformation like this

$$M(M(X)) \to M(X)$$

How? Simply pick the composition map to have $Y := M(Z)$, $X := M(M(X))$.

$$\textsf{mul}_X := \textsf{id}_{M(M(X))} {\,\texttt{>=>}\,} \textsf{id}_{M(X)}$$

where $f \texttt{>=>} g : X \to M(Z)$ is the Kleisli composition of $f : X \to M(Y)$ and $g : Y \to M(Z)$.

The properties of a monad follow from the fact that this map is unital (unitality laws of monads) and associative (associativity of monads).

# Internalized form for extension systems

Typically in programming languages you see these definitions for monads:

```haskell
-- Functoriality of m
fmap : (a -> b) -> m a -> m b

-- unit of the monad
return : a -> m a

-- multiplication of the monad
join : m (m a) -> a

-- notation: (>>=)
bind : (a -> m b) -> (m a -> m b)
bind : m a -> (a -> m b) -> m b
```

I would like to have an arrow like this: I would have to assume that my category is cartesian closed. And then, get an arrow like this:

$$
\begin{array}{l}
\textsf{join} : 1 \longrightarrow (M(M(A)) \Rightarrow A) \\
\textsf{bind} : 1 \longrightarrow (A \Rightarrow M(B)) \Rightarrow (M(A) \Rightarrow M(B)) \\
\textsf{bind} : 1 \longrightarrow M(A) \Rightarrow (A \Rightarrow M(B)) \Rightarrow M(B)
\end{array}
$$

because $A \Rightarrow (B \Rightarrow C) \cong B \Rightarrow (A \Rightarrow C)$ in any cartesian closed category (previous exercise!).

This "internal" arrow, turns out, can only (iff) be given if your monad is what is called a *strong monad*.

*(Claim.)* Every monad in Haskell is actually a strong monad.

*(Claim.)* Every monad on $\text{Prog}$ is also strong monad.

# Extension systems, explicitly

- The `X` $\mapsto$ `List<X>` endofunctor

```rust
// given an arrow $f : A -> List<B>$, ...
fn extend_f(xs: List<A>) -> List<B> {
    match xs {
        Empty => Empty
        Element(x, xs) => append(f(x), extend_f(xs))
    }
}

// in the theory:
fn extend_f(xs: List<A>) -> List<B> {
    let xxs: List<List<B>> = fmap(f, xs)
    join(xxs)
}
fn extend_f(xs: List<A>) -> List<B> {
    compose_(fmap_f)_join(xs)
}

```

- The `X` $\mapsto$ `Maybe<X>` endofunctor

```rust
// given an arrow $f : A -> Maybe<B>$, ...
fn extend_f(m: Maybe<A>) -> Maybe<B> {
    match m {
        Nothing => Nothing
        Just(x) => f(x)
    }
}
```

- The `X` $\mapsto$ `Either<E, X>` endofunctor

```rust
// given an arrow $f : A -> Either<E, B>$, ...
fn extend_f(m: Either<E, A>) -> Either<E, B> {
    match m {
        Left(e) => Left(e)
        p => f(p)
    }
}
```

- The `X` $\mapsto$ `Func<S, X>` endofunctor

```rust
// given an arrow $f : A -> Func<S, B>$, ...
fn extend_f(m: Func<S, A>>) -> Func<S, B> {
    |s: S|
        let a: A = m(s)
        f(a)(s)
}
```

# Some words about comonads

[Comonadic notions of computations](https://www.sciencedirect.com/science/article/pii/S1571066108003435)
