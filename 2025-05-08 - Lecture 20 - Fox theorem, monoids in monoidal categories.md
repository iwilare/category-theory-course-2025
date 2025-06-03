# 2025-05-08 - Lecture 20 (Fox's theorem, monoids, monads)

# Note:

Recall that in any category with products and terminal objects we have two "special" arrows, for any object $A$:

$$\begin{array}{rcl} \textsf{duplicate} := \langle \textsf{id}_A , \textsf{id}_A \rangle & : & A \to A \times A \\ !_A & : & A \to 1 \\ \end{array}$$

Monoidal categories do not come equipped by default with diagonal arrows $\delta$.

# Fox's theorem (1976)

We saw some examples of strict monoidal category.

Fox's theorem tells me sufficient (and necessary) conditions for the monoidal product to really be the *categorical product* (which *does* have a universal property, as we know), i.e., that

$$A \otimes B \cong A \times B$$

$$I \cong 1$$

*Theorem.*
Given a symmetric strict monoidal category, 1. the unit object $I$ is isomorphic to the terminal object $1$, and 2. the combine operation $A \otimes B$ is isomorphic to the product $A \times B$ precisely when the following things happen:

1. Every object $A$ has two arrows
    - $\delta_A : A \to A \otimes A \hspace{3em}$ (think of it as $\textsf{duplicate} : A \to A \times A$)
    - $\varepsilon_A : A \to I \hspace{5.3em}$ (think of it as $!_A : A \to 1$)

    In the language for string diagrams, we will represent $\delta_A$ these as:

    ```
                 A
             ------
            /
     A     /
    ------+
           \
            \    A
             ------
    ```
    instead of

    ```
        +------+  A
        |      |-----
        |      |
     A  |      |
    ----| δ_A  |
        |      |  A
        |      |-----
        |      |
        +------+
    ```

    And we will represent $\varepsilon_A$ as


    ```

       A
    --------+

    ```
    instead of

    ```
        +------+
     A  |      |
    ----| ε_A  |
        |      |
        +------+
    ```

    Remember! This arrow goes into $I$, which we already agreed we would not draw the wire for.

2. Each $(A , \delta_A, \varepsilon_A)$ is a commutative comonoid, i.e., these somewhat intuitive equations hold:
    - Unitality left,
    $$
    (\varepsilon_A \otimes \textsf{id}_A) \,; \delta_A = \textsf{id}_A
    $$
    - Unitality right,
    $$
    (\textsf{id}_A \otimes \varepsilon_A) \,; \delta_A = \textsf{id}_A
    $$
    - Associativity,
    $$
    (\textsf{id}_A \otimes \delta_A) \,; \delta_A = (\delta_A \otimes \textsf{id}_A) \,; \delta_A
    $$
    - Commutativity.
    $$
    \textsf{swap}_{A,A} \,; \delta_A = \delta_A
    $$
3. $\delta$ and $\varepsilon$ are "uniform" (i.e., well behaved w.r.t. monoidal structure):
  $$\delta_{A \otimes B} = (\delta_{A} \otimes \delta_{B}) \,; \textsf{id}_A \otimes \textsf{swap}_{A,B} \otimes \textsf{id}_B$$
  $$\varepsilon_{A \otimes B} = \varepsilon_{A} \otimes  \varepsilon_{B}$$
4. $\delta$ and $\varepsilon$ are "natural" (i.e., they slide nicely), for any $f : X \to Y$:
  $$f\,; \delta_{Y} = \delta_{X} \,; (f \otimes f)$$
  $$f\,; \varepsilon_{Y} = \varepsilon_{X}$$
  (This last condition fails in many interesting monoidal categories!! It's more like a feature than a bug.)

*Proof.* On the whiteboard, using string diagrams for monoidal categories.

# Definition: monoid object $A$ in a monoidal category $(I,\otimes)$

Imagine that someone gives me an object $A$.

A *monoid object structure* **on $A$** is a choice of two arrows:

- $\textsf{mul} : A \otimes A \to A$
- $\textsf{unit} : I \to A$

Such that the following string diagrammatic equations are satisfied:

1. $$(\textsf{unit} \otimes \textsf{id}_A) \,; \textsf{mul} = \textsf{id}_A$$
2. $$(\textsf{id}_A \otimes \textsf{unit}) \,; \textsf{mul} = \textsf{id}_A$$
3. $$(\textsf{mul} \otimes \textsf{id}_A) \,; \textsf{mul} = (\textsf{id}_A \otimes \textsf{mul}) \,; \textsf{mul}$$

*(Definition.)* A *monoid object* is a choice of an object $A$ with a *monoid object structure*. (We have to do all this precise/delicate thing because monoid object structures are not unique!)

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
- $\textsf{unit} : I \to \texttt{String} \\ $
  $\textsf{unit} : 1 \to \texttt{String} \\ $
  $\textsf{unit} : \texttt{Unit} \to \texttt{String}$
  ```rust
  fn unit(a: Unit) -> String {
    ""
  }
  ```
- Monoid law 1 essentially says that appending the empty string on the left behaves like the unitity program.
- Monoid law 2 essentially says that appending the empty string on the right behaves like the unitity program.
- Monoid law 3 essentially says that the two programs (with three arguments) that append together

*(Caveat). technically the category we are working with is not strict monoidal but only monoidal, which is a more complicated notion that we have not defined yet; we are doing some implicit strictification business here.*

## Example: a monoid object in $(\textsf{Cat}, \times, 1)$

This is precisely... a strict monoidal category!

*(Caveat). technically the category we are working with is not strict monoidal but only monoidal, which is a more complicated notion that we have not defined yet; we are doing some implicit strictification business here.*

## Example: a monoid object in $({[C,C]}, {\,;\,}, \textsf{id}_C)$

This is precisely... what people call a monad.

*(No caveat). the category we are working with really is strict monoidal! Everything is fine.*

*To Be Continued...*
