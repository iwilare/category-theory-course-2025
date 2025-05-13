# 2025-05-15 - Lecture 22 (Monads 2)

# Monads

# Extension form of monads

There is an equivalent *presentation* of monads.

Take an endofunctor $M : C \Rightarrow C$. (actually you don't even need the functor map, just a function on objects.)

An *extension system* for $M$ consists of the following choices:

- For every object $A$ of $C$, an arrow $\eta_A : A \to M(A)$,
- For every object $A,B$ of $C$, given an arrow $$f : A \to M(B),$$ a choice of an arrow $$\overline f : M(A) \to M(B).$$
- For every arrow $f : A \to B$, $$\eta_A \,; \overline{f} = f$$
- For every arrow $f : A \to M(B)$, $g : B \to M(C)$, $$\overline{f} \,; \overline{g} = \overline{(f \,; \overline{g})}$$

# These two presentations are equivalent

*(Theorem.)* Giving an extension system for an endofunctor $M : C \Rightarrow C$ is
   equivalent to giving a monad structure on $M : C \Rightarrow C$.

*Proof.*

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

}
```

- The `X` $\mapsto$ `Func<S, Pair<S, X>>` endofunctor

```rust
// given an arrow $f : A -> Func<S, Pair<S, B>>$, ...
fn extend_f(m: Func<S, Pair<S, A>>) -> Func<S, Pair<S, B>> {
    |a: A| {

    }
}
```

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
bind : (a -> m b) -> m a -> m b
```
