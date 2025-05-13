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

# Main examples of monads

The main examples of monads that we will see will be in our category $\text{Prog}$, calling $\textsf{join} := \textsf{mul}$ just for simplicity:

- *(The List/non-determinism monad.)* The `X` $\mapsto$ `List<X>` endofunctor, equipped with this monad structure:

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

- *(The Maybe/failure monad.)* The `X` $\mapsto$ `Maybe<X>` endofunctor, equipped with this monad structure:

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

- *(The Either/error monad.)* The `X` $\mapsto$ `Either<E, X>` endofunctor, equipped with this monad structure:

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

- *(The Reader monad.)* The `X` $\mapsto$ `Func<A, X>` endofunctor, equipped with this monad structure:

```rust
fn unit<X>(x: X) -> Func<A, X> {
    |r: A| x
}

fn join<X>(m: Func<A, Func<A, X>>) -> Func<A, X> {
    |a: A| m(a)(a)
}
```

- *(The Writer/logger monad.)* The `X` $\mapsto$ `Pair<String, X>` endofunctor, equipped with this monad structure:

```rust
fn unit<X>(x: X) -> Pair<String, X> {
    ("", x)
}

fn join<X>(m: Pair<String, Pair<String, X>>) -> Pair<String, X> {
    let (s1, (s2, x)) = m
    (s1 ++ s2, x) // string concatenation
}
```

- *(The State monad.)* The `X` $\mapsto$ `Func<A, Pair<A, X>>` endofunctor, equipped with this monad structure:

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

- *(The Probability Distribution monad.)* The `X` $\mapsto$ `List<Pair<f32,X>>` endofunctor, equipped with this monad structure:

```rust
fn unit<X>(x: X) -> List<X> {
    Element(Pair(1.0, x), Empty)
}

fn join<X>(xs: List<Pair<f32, List<Pair<f32, X>>>) -> List<Pair<f32, X>> {
    let choices: List<List<Pair<f32, X>>> =
        xs.map(|(v,cs): Pair<f32, List<Pair<f32, X>>>|
               cs.map((w, x) => (v * w, x)))
    choices.join()
}

// Technically, we should ensure that every arrow of this form preserves these two invariants:
// - there is no element in each list with value 0,
// - the sum of all weights is always exactly 1.0.
```

# Fundamental idea: effectful arrows

The idea is that now we "enrich" our arrows by considering arrows not just like $X \to Y$, but always in the form

$$X \to M(Y)$$

where $M$ is a monad that we can pick. Picking different monads gives us different "effects".

We call arrows in this form "Kleisli arrows" or "effectful arrows".

- **(Failure effect.)** In the case of `X` $\mapsto$ `Maybe<X>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> Maybe<B> { ... }
  ```
  **Idea:** arrows are programs that can fail.
- **(Non-deterministic effect.)** In the case of `X` $\mapsto$ `List<X>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> List<B> { ... }
  ```
  **Idea:** arrows are programs that return multiple results "non-deterministically".
- **(Errors/exceptions effect.)** In the case of `X` $\mapsto$ `Either<E, X>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> Either<E, B> { ... }
  ```
  **Idea:** arrows are programs that can fail, with an explicit reason why (given by the type `E`).
- **(Reading from the environment effect.)** In the case of `X` $\mapsto$ `Func<S, X>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> Func<S, Pair<S, B>> { ... }
  fn f(x: Pair<S, A>) -> Pair<S, B> { ... } // by the iso of types of arrows into an exponential object
  fn f(s: S, a: A) -> Pair<S, B> { ... }
  ```
  **Idea:** arrows are programs that can read from an environment.
- **(Logging/writing effect.)** In the case of `X` $\mapsto$ `Pair<String, X>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> Pair<String, B> { ... }
  ```
  **Idea:** arrows are programs that return a value and also ask to write something to the console.
- **(State effect.)** In the case of `X` $\mapsto$ `Func<S, Pair<S, X>>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> Func<S, Pair<S, B>> { ... }
  fn f(x: Pair<S, A>) -> Pair<S, B> { ... }
  fn f(s: S, a: A) -> Pair<S, B> { ... }
  ```
  **Idea:** arrows are programs that can read and write to a global shared state.
- **(Probabilistic effect.)** In the case of `X` $\mapsto$ `List<Pair<f32, X>>`, a Kleisli arrow is a program
  ```rust
  fn f(x: A) -> List<Pair<f32, X>> { ... }
  ```
  **Idea:** arrows are programs that return a probability distribution of possible values of `X`. You can think of `List` as being the special case where every distribution is uniform, i.e., every value has equal likelihood of being the "selected" one.

# Monad laws for our examples

> Monad laws: (monoid laws for $M$ viewed as a monoid)
> 1. $(\textsf{unit} * \textsf{id}_M) \,; \textsf{mul} = \textsf{id}_M$
> 2. $(\textsf{id}_M * \textsf{unit}) \,; \textsf{mul} = \textsf{id}_M$
> 3. $ (\textsf{mul} * \textsf{id}_M) \,; \textsf{mul} = (\textsf{id}_M * \textsf{mul}) \,; \textsf{mul}$

Note! These are equalities between arrows in the category $[C,C]$: but arrows in this category are natural transformations, and equalities between natural transformations were defined to be equalities between families of arrows, so for each object $X$ we have that $\alpha_X = \beta_X$.

> 1. for every $X$, $(\textsf{unit} * \textsf{id}_M)_X \,; \textsf{mul}_X = \textsf{id}_{M(X)}$
> 2. for every $X$, $(\textsf{id}_M * \textsf{unit})_X \,; \textsf{mul}_X = \textsf{id}_{M(X)}$
> 3. for every $X$, $ (\textsf{mul} * \textsf{id}_M)_X \,; \textsf{mul}_X = (\textsf{id}_M * \textsf{mul})_X \,; \textsf{mul}_X$

Remember the definition of horizontal composition of natural transformations (Lecture 16):

> Given two natural transformations:
> - $\alpha : F \longrightarrow F'$
> - $\beta : G \longrightarrow G'$
>
> I cook up a new natural transformation like this
> - $\gamma : (F\,;G) \longrightarrow (F'\,; G')$
>
> which we will denote as $\gamma := \alpha * \beta$. This natural transformation, called "horizontal composition of $\alpha,\beta$ is defined like this:
> $$
> \begin{array}{rcl}
> (\alpha \ast \beta)_X &:=& G(\alpha_X) \,; \beta_{F'(X)}  \\
> \end{array}
> $$


Now, $\textsf{unit} : \textsf{id}_C \longrightarrow M, \quad \textsf{mul} : (M\,;M) \longrightarrow M, \quad \textsf{id}_M : M \longrightarrow M$.

Moreover, remember that $(\textsf{id}_M)_X := \textsf{id}_{M(X)}$, since when we write $\textsf{id}_M$ we really mean the identity arrow in $[C,C]$ on the object $M$, but an arrow, whether it's an identity or not, it's a natural transformation so a family of arrows again.

Let's unpack all horizontal compositions:

> - $\textsf{unit} * \textsf{id}_M : M = \textsf{id}_C \,; M \to M \,; M \quad$ hence $F = \textsf{id}_C$, $F' = G = G' = M$ in the def. of horiz. comp.
> - $\textsf{id}_M * \textsf{unit} : M = M \,; \textsf{id}_C \to M \,; M \quad$ hence $G = \textsf{id}_C$, $F' = F = G' = M$ in the def. of horiz. comp.
> - $\textsf{mul} * \textsf{id}_M : (M \,; M) \,; M \to M \,; M \quad$ hence $F = M\,;M$, $F' = G = G' = M$ in the def. of horiz. comp.
> - $\textsf{id}_M * \textsf{mul} : M \,; (M \,; M) \to M \,; M \quad $ hence $G = M\,;M$, $F' = F = G' = M$ in the def. of horiz. comp.

Following the definition of horizontal composition:

> - $(\textsf{unit} * \textsf{id}_M)_X = M(\textsf{unit}_X) \,; \textsf{id}_{M(X)} = M(\textsf{unit}_X)$
> - $(\textsf{id}_M * \textsf{unit})_X  = M(\textsf{id}_{M(X)}) \,; \textsf{unit}_{M(X)} = \textsf{unit}_{M(X)}$
> - $ (\textsf{mul} * \textsf{id}_M)_X = M(\textsf{mul}_X) \,;  \textsf{id}_{M(M(X))} = M(\textsf{mul}_X)$
> - $(\textsf{id}_M * \textsf{mul})_X = M(\textsf{id}_{M(X)}) \,; \textsf{mul}_{M(M(X))} = \textsf{mul}_{M(M(X))}$

Then we substitute them back in the original definition in order to get **the monad laws.**

> ***Monad laws, in elementary form:***
> 1. for every $X$, $M(\textsf{unit}_X) \,; \textsf{mul}_X = \textsf{id}_{M(X)}$ as arrows $M(M(X)) \to M(X)$
> 2. for every $X$, $\textsf{unit}_{M(X)} \,; \textsf{mul}_X = \textsf{id}_{M(X)}$ as arrows $M(M(X)) \to M(X)$
> 3. for every $X$, $M(\textsf{mul}_X) \,; \textsf{mul}_X = \textsf{mul}_{M(X)} \,; \textsf{mul}_X$ as arrows $M(M(M(X))) \to M(X)$

## Example

We illustrate the idea using $M := \textsf{List}$.


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

- *(Left identity.)* for every $X,Y$ and $g : X \to Y$ then $\textsf{id}_X \,; g = g$.

  for every $X,Y$ and $g : X \to M(Y)$ then $\textsf{unit}_X \,; M(g) \,; \textsf{mul}_Y  = g$.

    $$\begin{array}{lll}
        & \textsf{unit}_X \,; M(g) \,; \textsf{mul}_Y & \text{(naturality of \text{unit})} \\
      = & g \,; \textsf{unit}_Y \,; \textsf{mul}_Y & \text{(first monad law)} \\
      = & f &
      \end{array}$$

  Remember that $\textsf{unit}$ must be a natural transformation: so, for every $X,Y$ and $f : X \to Y$,

$$
    \begin{array}{ccccccccc}
    \phantom{\textsf{id}_C(f) M(f)} X & \xrightarrow{\phantom{iii}\textsf{unit}_X\phantom{iii}} & M(X) \phantom{M(f)} & \\
    {\textsf{id}_C(f) = f} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {M(f)}\\
    \phantom{\textsf{id}_C(f) M(f)} Y & \xrightarrow{\phantom{iii}\textsf{unit}_Y \phantom{iii}} & M(Y) \phantom{M(f)} \\
    \end{array}
$$

  We used this hypothesis above.

- *(Right identity.)* for every $X,Y$ and $f : X \to Y$ then $f \,; \textsf{id}_Y = f$.

  for every $X,Y$ and $f : X \to M(Y)$ then
    $$\begin{array}{lll}
        & f \,; M(\textsf{unit}_Y) \,; \textsf{mul}_Y & \text{(second monad law)} \\
      = & f \,; \textsf{id}_{M(Y)} & \\
      = & f &
      \end{array}$$

- *(Associativity.)* Exercise. You need to use associativity.

### At the end of the day, we obtained an operation that composes "effectul arrows" together.
