# 2025-05-09 - Lecture 21 (Monads)

# Monads

*A monad is a monoid object in $({[C,C]}, {\,;\,}, \textsf{id}_C)$.*

> *A monoid object in a category $D$ which is strict monoidal $(\otimes,I)$
>  is a choice of an object $M$ of $D$ along with a choice of two arrows in the category $D$*
>  - $\textsf{mul} : M \otimes M \to M$ and
>  - $\textsf{unit} : I \to M$
>
>  such that certain equations that you know hold.


> *A monoid in the category $[C,C]$ which is strict monoidal $({\,;\,}, \textsf{id}_C)$
> is a choice of an endofunctor $M : C \Rightarrow C$ along with a choice of two arrows in the category $[C,C]$*
> - $\textsf{mul} : (M \,; M) \to M$ and
> - $\textsf{unit} : \textsf{id}_C \to M$
>
> such that certain equations that you know hold.

> *A monoid in the category $[C,C]$ which is strict monoidal $({\,;\,}, \textsf{id}_C)$
> is a choice of an endofunctor $M : C \Rightarrow C$ along with a choice of two arrows in the category $[C,C]$*
> - a natural transformation $\textsf{mul}_X : M(M(X)) \to M(X)$ and
> - a natural transformation $\textsf{unit}_X : \textsf{id}_C(X) = X \to M(X)$
>
> such that certain equations that you know hold.


> The equations:
> 1. $(\textsf{unit} \otimes \textsf{id}_M) \,; \textsf{mul} = \textsf{id}_M$
> 2. $(\textsf{id}_M \otimes \textsf{unit}) \,; \textsf{mul} = \textsf{id}_M$
> 3. $ (\textsf{mul} \otimes \textsf{id}_M) \,; \textsf{mul} = (\textsf{id}_M \otimes \textsf{mul}) \,; \textsf{mul}$

> The equations, now writing the $\otimes$ as the horizontal composition $\alpha * \beta$ of natural transformations:
> 1. $(\textsf{unit} * \textsf{id}_M) \,; \textsf{mul} = \textsf{id}_M$
> 2. $(\textsf{id}_M * \textsf{unit}) \,; \textsf{mul} = \textsf{id}_M$
> 3. $ (\textsf{mul} * \textsf{id}_M) \,; \textsf{mul} = (\textsf{id}_M * \textsf{mul}) \,; \textsf{mul}$

# Examples of monads

The main examples of monads that we will see will be in our category $\text{Prog}$, calling $\textsf{join} := \textsf{mul}$ just for simplicity:

- The `X` $\mapsto$ `List<X>` endofunctor

```rust
fn unit<X>(x: X) -> List<X> {
    Element(x, Empty)
}

fn join<X>(xs: List<List<X>>) -> List<X> {
    match xs {
        Empty => Empty
        Element(x, xs) => append(x, join(xs))
    }
}
```

- The `X` $\mapsto$ `Maybe<X>` endofunctor

```rust
fn unit<X>(x: X) -> Maybe<X> {
    Just(x)
}

fn join<X>(m: Maybe<Maybe<X>>) -> Maybe<X> {
    match m {
        Nothing => Nothing
        Just(x) => x
    }
}
```

- The `X` $\mapsto$ `Either<E, X>` endofunctor

```rust
fn unit<X>(x: X) -> Either<E, X> {
    Right(x)
}

fn join<X>(m: Either<E, Either<E, X>>) -> Either<E, X> {
    match m {
        Left(e) => Left(e)
        p => p
    }
}
```

- The `X` $\mapsto$ `Func<A, X>` endofunctor

```rust
fn unit<X>(x: X) -> Func<A, X> {
    |r: A| x
}

fn join<X>(m: Func<A, Func<A, X>>) -> Func<A, X> {
    |a: A| m(a)(a)
}
```

- The `X` $\mapsto$ `Func<A, Pair<A, X>>` endofunctor

```rust
fn unit<X>(x: X) -> Func<A, Pair<A, X>> {
    |r: A| (r, x)
}

//         Func<A, Pair<A, X>>,   now replacing X with Func<A, Pair<A, X>>
//         Func<A, Pair<A, Func<A, Pair<A, X>>>
fn join<X>(m: Func<A, Pair<A, Func<A, Pair<A, X>>>) ->
    Func<A, Pair<A, X>> {
      |a: A| {
        let (b: A, f: Func<A, Pair<A, X>>) = m(a)
        f(b)
      }
}
```

# Monad laws for our examples

- `List` monad:

> Monad laws: (monoid laws for $M$ viewed as a monoid)
> 1. $(\textsf{unit} * \textsf{id}_M) \,; \textsf{mul} = \textsf{id}_M$
> 2. $(\textsf{id}_M * \textsf{unit}) \,; \textsf{mul} = \textsf{id}_M$
> 3. $ (\textsf{mul} * \textsf{id}_M) \,; \textsf{mul} = (\textsf{id}_M * \textsf{mul}) \,; \textsf{mul}$

### Monad law number 1 (unitality)

```rust
                              id_M = id_List
    List = List ; id_Prog --------------------> List

                       =

    List -----------\
                     \
                      +----------------- List
                     /
             x------/
```

Program on arrows of List (the "fmap" on unit_X ), using curly brackets for the program on arrows/fmap and angled brackets for the program on objects.

```
                                 List(unit<X>)
    List<X> = List<id_Prog<X>> ---------------> List<List<X>>
          |                                         |
          |                                         | join<X>
          |                                         |
          |                                         V
          +-------------------------------------> List<X>
                  id_List<X>
```

For every $X$, this triangle commutes, i.e.,

```
List(unit<X>) ; join<X> = id_List<X>
```

i.e., for every list `xs: List<X>`

```
compose_(fmap_unit)_join(xs) = id_List<X>(xs) = xs
```

This is the idea:

```rust
     [a, b, c, d, e, ... ]

        // fmap unit on top of it, this is what unit does:

     [[a], [b], [c], [d], [e], ... ]

       // then, join:

     [a, b, c, d, e, ... ]
```

### Monad law number 2 (unitality)


```rust
                              id_M = id_List
    List = id_Prog ; List --------------------> List

                       =

             x------\
                     \
                      +----------------- List
                     /
    List -----------/
```

Program on arrows of List (the "fmap" on unit_X ), using curly brackets for the program on arrows/fmap and angled brackets for the program on objects.

```
                      unit<List<X>>
       List<X> ----------------------------> List<List<X>>
          |                                         |
          |                                         | join<X>
          |                                         |
          |                                         V
          +-------------------------------------> List<X>
                  id_List<X>
```

For every $X$, this triangle commutes, i.e.,

```
unit<List<X>> ; join<X> = id_List<X>
```

i.e., for every list `xs: List<X>`

```
compose_unit_join(xs) = xs
```

This is the idea:

```rust
     [a, b, c, d, e, ... ]

        // just do unit (on List<X>!):

     [[a, b, c, d, e, ... ]]

        // then, join:

     [a, b, c, d, e, ... ]
```

### Monad law number 3 (associativity)

```rust
     [
      [[a1, a2, a3], [b1, b2], [c2, c3]],
      [[d1, d2], [e1, e2]],
      ...
     ]: List<List<List<i32>>>
     // ^^^^^^^^^
     //   collapse these two first

     // do the outer join first.

     [
      [a1, a2, a3], [b1, b2], [c2, c3],
      [d1, d2], [e1, e2],
      ...
     ]: List<List<i32>>

        // do the join
     [
      a1, a2, a3, b1, b2, c2, c3,
      d1, d2, e1, e2,
      ...
     ]
```

```rust
     [
      [[a1, a2, a3], [b1, b2], [c2, c3]],
      [[d1, d2], [e1, e2]],
      ...
     ]: List<List<List<i32>>>
     //      ^^^^^^^^^
     //   collapse these two first

     [
      [a1, a2, a3, b1, b2, c2, c3],
      [d1, d2, e1, e2],
      ...
     ]: List<List<i32>>

        // do the join

     [
      a1, a2, a3, b1, b2, c2, c3,
      d1, d2, e1, e2,
      ...
     ]
```


# Monads are not compositional

Given two monads $M, M' : C \Rightarrow C$, their composition $M\,;M'$ does not necessarily carry monad structure.

# The Kleisli category of a monad $M$

Imagine someone gives me a category $C$ with a monad $M : C \Rightarrow C$.

We're going to define a new category $\textsf{Kleisli}(M)$.

- *(Objects.)* The same objects of $C$.
- *(Arrows.)* There is an arrow from $X$ to $Y$ in $\textsf{Kleisli}(M)$ whenever there is an arrow $X \to M(Y)$.
- *(Identities.)* Given an object $X$, I need to pick an arrow from $X$ to $X$ in $\textsf{Kleisli}(M)$. But this amounts to having to pick an arrow $X \to M(X)$. I pick this arrow to be $\textsf{unit}_X : X \to M(X)$.
- *(Composition.)* For every three objects $X,Y,Z$, if I am given two arrows from $X$ to $Y$ and from $Y$ to $Z$ *in* $\textsf{Kleisli}(M)$ then I need to construct a new arrow from $X$ to $Z$ in $\textsf{Kleisli}(M)$.
  Effectively, it means that, given
  - $f : X \to M(Y)$
  - $g : Y \to M(Z)$,

  I need to give you an arrow
  - $X \to M(Z).$

  The arrow I give you is this one:

  - $M(g) \quad \quad : M(Y) \to M(M(Z))$ (using the "fmap")
  - $f \,; M(g) \quad : X \to M(M(Z))$
  - $f \,; M(g) \,; \textsf{mul}_Z \quad : X \to M(Z)$

- *(Left identity.*) for every $X,Y$ and $g : X \to Y$ then $\textsf{id}_X \,; g = g$.

  for every $X,Y$ and $g : X \to M(Y)$ then $\textsf{unit}_X \,; M(g) \,; \textsf{mul}_Z  = g$.

- *(Right identity.*) for every $X,Y$ and $f : X \to Y$ then $f \,; \textsf{id}_Y = f$.

  for every $X,Y$ and $f : X \to M(Y)$ then $f \,; M(\textsf{unit}_Y) \,; \textsf{mul}_Z = f$.

### At the end of the day, we obtained for "free" an operation that composes "effectul arrows" together.

- In the case of `X` $\mapsto$ `Maybe<X>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> Maybe<B> { ... }
  ```
- In the case of `X` $\mapsto$ `List<X>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> List<B> { ... }
  ```
- In the case of `X` $\mapsto$ `Either<E, X>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> Either<E, B> { ... }
  ```
- In the case of `X` $\mapsto$ `Func<S, Pair<S, X>>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> Func<S, Pair<S, B>> { ... }
  fn f(x: Pair<S, A>) -> Pair<S, B> { ... }
  fn f(s: S, a: A) -> Pair<S, B> { ... }
  ```

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
