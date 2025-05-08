# 2025-05-08 - Lecture 20 (Fox's theorem, monoids, monads)

# Note:

Recall that in any category with products and terminal objects we have two "special" arrows, for any object $A$:

$$\begin{array}{rcl} \textsf{duplicate} := \langle \textsf{id}_A , \textsf{id}_A \rangle & : & A \to A \times A \\ !_A & : & A \to 1 \\ \end{array}$$

Monoidal categories do not come equipped by default with diagonal arrows $\delta$.

# Fox's theorem (1976)

We saw some examples of strict monoidal category.

Given a symmetric strict monoidal category, the unit object and combine operations $(I,\otimes)$ are equivalent to the terminal object and the combine operation $(1,\times)$ if the following happens:

1. Every object $X$ has two maps
    - $\delta_A : A \to A \otimes A \hspace{3em}$ (think of it as $\textsf{duplicate} : A \to A \times A$)
    - $\varepsilon_A : A \to I \hspace{5.3em}$ (think of it as $!_A : A \to 1$)
2. Each $(A , \delta_A, \varepsilon_A)$ is a commutative comonoid, i.e., these somewhat intuitive equations hold:
    - Unitality left,
    - Unitality right,
    - Associativity,
    - Commutativity.
3. $\delta$ and $\varepsilon$ are "uniform" (i.e., well behaved w.r.t. monoidal structure):
  $$\delta_{A \otimes B} = (\delta_{A} \otimes \delta_{B}) \,; \textsf{id}_A \otimes \textsf{swap}_{A,B} \otimes \textsf{id}_B$$
  $$\varepsilon_{A \otimes B} = \varepsilon_{A} \otimes  \varepsilon_{B}$$
4. $\delta$ and $\varepsilon$ are "natural" (i.e., they slide nicely), for any $f : X \to Y$:
  $$f\,; \delta_{Y} = \delta_{X} \,; (f \otimes f)$$
  $$f\,; \varepsilon_{Y} = \varepsilon_{X}$$
  (This last condition fails in many interesting monoidal categories!! It's more like a feature than a bug.)


# Definition: monoid object $A$ in a monoidal category $(I,\otimes)$

Imagine that someone gives me an object $A$.

A *monoid object structure* on $A$ is a choice of two arrows:

- $\textsf{mul} : A \otimes A \to A$
- $\textsf{unit} : I \to A$

Such that the following string diagrammatic equations are satisfied:

1. $$(\textsf{unit} \otimes \textsf{id}_A) \,; \textsf{mul} = \textsf{id}_A$$
2. $$(\textsf{id}_A \otimes \textsf{unit}) \,; \textsf{mul} = \textsf{id}_A$$
3. $$ ((\textsf{mul} \otimes \textsf{id}_A) \otimes \textsf{id}_A) \,; \textsf{mul} = ((\textsf{id}_A \otimes \textsf{mul}) \otimes \textsf{id}_A) \,; \textsf{mul}$$

*(Definition.)* A *monoid pbject* is a choice of an object $A$ with a *monoid object structure*. (We have to do all this precise/delicate thing because monoid object structures are not unique!)

## Example: a monoid in $(\textsf{Prog}, \times, 1)$

This is precisely what a monoid is the way we have defined it in Lecture 3.

Example: I pick the object $A :=$ `String`. Then, the arrows that I pick are

- $\textsf{mul} : \texttt{String} \otimes \texttt{String} \to \texttt{String} \\ $
  $\textsf{mul} : \texttt{String} \times \texttt{String} \to \texttt{String}$
  ```rust
  fn mul(a: Pair<String, String>) -> String { ... }
  fn mul(a: String, b: String) -> String {
    a + b // string concatenation
  }
  ```
- $\textsf{ident} : I \to \texttt{String} \\ $
  $\textsf{ident} : 1 \to \texttt{String} \\ $
  $\textsf{ident} : \texttt{Unit} \to \texttt{String}$
  ```rust
  fn ident(a: Unit) -> String {
    ""
  }
  ```
- Monoid law 1 essentially says that appending the empty string on the left behaves like the identity program.
- Monoid law 2 essentially says that appending the empty string on the right behaves like the identity program.
- Monoid law 3 essentially says that the two programs (with three arguments) that append together

*(Caveat). technically the category we are working with is not strict monoidal but only monoidal, which is a more complicated notion that we have not defined yet; we are doing some implicit strictification business here.*

## Example: a monoid object in $(\textsf{Cat}, \times, 1)$

This is precisely... a strict monoidal category!

*(Caveat). technically the category we are working with is not strict monoidal but only monoidal, which is a more complicated notion that we have not defined yet; we are doing some implicit strictification business here.*

## Example: a monoid object in $({[C,C]}, {\,;\,}, \textsf{id}_C)$

This is precisely... what people call a monad.

*(No caveat). the category we are working with really is strict monoidal! Everything is fine.*

# Monads

*A monad is a monoid in $({[C,C]}, {\,;\,}, \textsf{id}_C)$.*

> *A monoid in a category $D$ which is strict monoidal $(\otimes,I)$
>  is a choice of an object $M$ of $D$ along with a choice of two arrows in the category $D$*
>  - $\textsf{mul} : M \otimes M \to M$ and
>  - $\textsf{ident} : I \to M$
>
>  such that certain equations hold.

> *A monoid in the category $[C,C]$ which is strict monoidal $({\,;\,}, \textsf{id}_C)$
> is a choice of an endofunctor $M : [C,C]$ along with a choice of two arrows*
> - $\textsf{mul} : (M \,; M) \to M$ and
> - $\textsf{ident} : \textsf{id}_C \to M$
>
> such that certain equations hold.

> *A monoid in the category $[C,C]$ which is strict monoidal $({\,;\,}, \textsf{id}_C)$
> is a choice of an endofunctor $M : [C,C]$ along with a choice of two arrows in the category $[C,C]$*
> - $\textsf{mul} : (M \,; M) \to M$ and
> - $\textsf{ident} : \textsf{id}_C \to M$
>
> such that certain equations hold.

> *A monoid in the category $[C,C]$ which is strict monoidal $({\,;\,}, \textsf{id}_C)$
> is a choice of an endofunctor $M : [C,C]$ along with a choice of two arrows in the category $[C,C]$*
> - a natural transformation $\textsf{mul}_X : M(M(X)) \to M(X)$ and
> - a natural transformation $\textsf{ident}_X : \textsf{id}_C(X) = X \to M(X)$
>
> such that certain equations hold.

# The Kleisli category of a monad $M$

Imagine someone gives me a category $C$ with a monad $M : [C,C]$.

We're going to define a new category $\textsf{Kleisli}(M)$.

- *(Objects.)* The same objects of $C$.
- *(Arrows.)* There is an arrow from $X$ to $Y$ in $\textsf{Kleisli(M)}$ whenever there is an arrow $X \to M(Y)$.
- *(Identities.)* Given an object $X$, I need to pick an arrow from $X$ to $X$ in $\textsf{Kleisli(M)}$. But this amounts to having to pick an arrow $X \to M(X)$. I pick this arrow to be $\textsf{ident}_X : X \to M(X)$.
- *(Composition.)* For every three objects $X,Y,Z$, if I am given two arrows from $X$ to $Y$ and from $Y$ to $Z$ *in $\textsf{Kleisli}(M)$ then I need to construct a new arrow from $X$ to $Z$ in $\textsf{Kleisli}(M)$.
  Effectively, it means that, given
  - $f : X \to M(Y)$
  - $g : Y \to M(Z)$,

  I need to give you an arrow
  - $X \to M(Z).$

  The arrow I give you is this one:

# Extension form of monads

There is an equivalent *presentation* of monads.
