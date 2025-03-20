# 2025-03-13 - Lecture 8 (Initial, terminal, exponential objects)

## Today's plan

- Define initial and terminal objects
- Explore examples in our categories

# Definition: initial object

An object $A$ of a category $C$ is called an *initial object* if the following condition holds:

1. *(Existence of arrows to every object.)* For every object $H$ in $C$, there is an arrow $!_H: A \to H$.
2. *(Uniqueness of this arrow.)* for every object $H$, any arrow $h : A \to H$ is actually equal to $h = {!_H}$.

```
   .      .     .      .    .
   H1     H2    H3     A    H5
    ^           ^           ^
     \    ...   |   ...    /
      \         |         /
       \        |        /
        \       |       /
         \      |      /
          \     |     /
           \    |    /
            \   |   /
             \  |  /
              \ | /
               \|/
                .
                A
```


In other words, an initial object is one that has a unique arrow to every other object in the category.

> Initial objects are sometimes denoted as $0$.

# Definition: terminal object

> An object $A$ of a category $C$ is called a *terminal object* exactly when $A$ is an initial object in $C^\textsf{op}$.

An object $A$ of a category $C$ is called a *terminal object* if the following condition holds:

- *(Existence of arrows from every object.)* For every object $H$ in $C$, there is an arrow $()_H: H \to A$.
- *(Uniqueness of this arrow.)* for every object $H$, any arrow $h : H \to A$ is actually equal to $h = {()_H}$.

In other words, a terminal object is one that has a unique arrow from every other object in the category.

> Terminal objects are sometimes denoted as $1$.

**(End of definitions.)**

These definitions are duals of each other - they have the same structure but with the direction of arrows reversed:

> *Claim.* An object $A$ is initial in $C$ exactly when $A$ is terminal in the category $C^{\textsf{op}}$, and an object $A$ is terminal in $C$ exactly when it is initial in $C^{\textsf{op}}$.

# Examples: Initial and terminal objects in $\text{Prog}$

Recall that `Prog` has as objects all the types we've listed and all total programs between as arrows.

### Initial object in $\text{Prog}$

We claim that the *empty type* is an initial object in `Prog`. We define it as:

```rust
enum Empty {
    // No variants!
}
```

*Proof.*

1. *(Existence of arrows to every object.)* For any type `H`, we must show there exists exactly one Rust program from `Empty` to `H`.

  Let's define such a function:

  ```rust
  fn empty_to_h(e: Empty) -> H {
      match e {
          // There are no cases to match!
      }
  }
  ```

  This is known as *vacuous pattern matching*. Since there are no values of type `Empty`, this function can never actually be called with a value. However, the type system still considers this a valid total function.

2. *(Uniqueness of this arrow.)* Moreover, this is the *only* possible function. If we had any other function `fn f(e: Empty) -> H`:

    Let's way I want to show that this `f` is program equivalent to `empty_to_h`.

    I have to show that for every possible value `v:Empty` then the two functions agree when they are called on `v`.

    But there are no values of type `v:Empty`! So I'm done.

### Terminal object in $\text{Prog}$

We claim that the *unit type* is a terminal object in `Prog`. We define it as:

```rust
struct Unit {
    // No fields!
}

// A value of type Unit: Unit { }

// // Alternatively:
// enum Unit {
//     One // ()
// }

// A value of type Unit: One
```

*Proof.*

- *(Existence of unique arrows from every object.)* For any type `H`, we must show there exists exactly one function from `H` to `Unit`.

  Let's define such a function:

  ```rust
  fn h_to_unit(x: H) -> Unit {
      Unit {}  // or Unit::UnitValue
  }
  ```

  This function simply ignores its input and returns the only possible value of type `Unit`.

 - *(Uniqueness of this arrow.)* This is the *only* possible function, because there's only one possible value to return, and a total function must return something.

## Initial and terminal objects in $(\N, \le)$

> RECALL:
> - Objects are natural numbers.
> - There is an arrow from $n$ to $m$ if and only if $n \le m$.

### Initial object in $(\N, \le)$

The initial object is $0$.

*Proof.*

- *(Existence of unique arrows to every object.)* For every natural number $n$, there exists exactly one arrow from $0$ to $n$, which corresponds to the fact that $0 \le n$ for all natural numbers $n$.

### Terminal object in $(\N, \le)$

There is no terminal object in $(\N, \le)$.

*Proof.*

For an object $T$ to be terminal, we would need a unique arrow from every other object to $T$. This would mean that for every natural number $n$, we must have $n \le T$.

However, for any candidate terminal number $T$, there is always a larger number $T+1$ such that $T+1 > T$, so there is no arrow from $T+1$ to $T$. Therefore, no natural number can be a terminal object in this category.

## Initial and terminal objects in $(\N, \textsf{divides})$

> RECALL:
> - Objects are natural numbers.
> - There is an arrow from $n$ to $m$ if and only if $n$ divides $m$ (i.e., $m = k \cdot n$ for some $k$).

### Initial object in $(\N, \textsf{divides})$

The initial object is $1$.

*Proof.*

- *(Existence of unique arrows to every object.)* For every natural number $n$, there exists exactly one arrow from $1$ to $n$, which corresponds to the fact that $1$ divides every natural number (since $n = n \cdot 1$).

### Terminal object in $(\N, \textsf{divides})$

There is a terminal object, and it's $0$.

*Proof.*

To claim that $0$ is terminal, we need to show that for every other number $n$, we have that $n$ divides $0$.
So, we have to show, by definition of "divides", that there exists a number $k$ such that $0 = n * k$, so I can pick exactly $k$ to be zero.

## Initial and terminal objects in $(\text{BExpr}, \to)$

> RECALL:
> - Objects are boolean expressions.
> - There is an arrow from expression $P$ to expression $Q$ if and only if $P$ implies $Q$, written $P \to Q$, i.e., according to the following truth table:
>
>    | P | Q | P $\to$ Q |
>    |--|--|--|
>    0 | 0 | 1
>    0 | 1 | 1
>    1 | 0 | 0
>    1 | 1 | 1


### Initial object in $(\text{BExpr}, \to)$

The initial object is `False` (`F`).

*Proof.*

- *(Existence of arrows to every object.)* For every boolean expression $P$, there exists exactly one arrow from `F` to $P$, which corresponds to the fact that falsehood implies anything. This is the principle of "ex falso quodlibet" (from falsehood, anything follows).

### Terminal object in $(\text{BExpr}, \to)$

The terminal object is `True` (`T`).

*Proof.*

- *(Existence of arrows from every object.)* For every boolean expression $P$, there exists exactly one arrow from $P$ to `T`, which corresponds to the fact that anything implies truth.

# Notation for initial and terminal objects

```rust
enum Empty {

}

// There are no constructors!

struct Unit {

}

// There is only one constructor:
// -> Unit {}
```

> For this reason, the initial objects and terminal objects in a category are sometimes denoted with $0$ for initial and $1$ for terminal objects.

# Initial and terminal objects are not unique

Just like products, initial and terminal objects are not unique in a category. However, any two initial objects are isomorphic to each other, and any two terminal objects are isomorphic to each other.

For example, in `Prog`, we could define an alternative empty type:

```rust
enum AnotherEmpty {
    // Still no variants!
}
```

This is also an initial object. Similarly, we could define:

```rust
struct AnotherUnit {
    // Still no fields!
}
```

This is also a terminal object.

While these types are technically different, they behave identically in the category. We say they are "unique up to isomorphism."

# Universal property of terminal objects

Any two objects $P$ and $Q$ that satisfy the property of being terminal are isomorphic.

*Proof*.

Assume that we are given $P$ and $Q$, such that we know that both of them are terminal.


*Task objective.* Prove that $P \cong Q$.

*Task objective.* Build two arrows $\textsf{p2q} : P \to Q, \textsf{q2p} : Q \to P$, show that $$\textsf{p2q} \,; \textsf{q2p} = \textsf{id}_P, \quad \textsf{q2p} \,; \textsf{p2q} = \textsf{id}_Q.$$

Solution:

Remember that
- $\textsf{()}_{P} : P \to Q$
- $\textsf{()}_{Q} : Q \to P$

Then, the conversion arrows are the following ones:

$$
\begin{array}{ll}
    \textsf{p2q} := \textsf{()}_{P} : P \to Q \\
    \textsf{q2p} := \textsf{()}_{Q} : Q \to P \\
\end{array}
$$

*Small remark.* There might be arrows $f : P \to P$ on some object $P$ such that $f$ is just not the identity.


*Lemma.* Given an arrow $h : P \to P$, then $h = \textsf{id}_P$. *Proof.* By definition, any arrow $h$ into the terminal $P$, no matter the source (we chose $H$ to be $P$ again in this case), is equal to $()_P$, i.e., $()_P = h$. In particular, $()_P = \textsf{id}_P$, hence $h = \textsf{id}_P$ by transitivity.

We show that $\textsf{()}_{P}  \,;  \textsf{()}_{Q}  = \textsf{id}_{P}$. Note that $\textsf{()}_{P}  \,;  \textsf{()}_{Q} : P \to P$ has precisely the type such that this lemma applies. Therefore $\textsf{()}_{P}  \,;  \textsf{()}_{Q}  = \textsf{id}_{P}$, done!


We show that $\textsf{()}_{Q}  \,;  \textsf{()}_{P}  = \textsf{id}_{Q}$. Note that $\textsf{()}_{Q}  \,;  \textsf{()}_{P} : Q \to Q$ has precisely the type such that this lemma applies. Therefore $\textsf{()}_{Q}  \,;  \textsf{()}_{P}  = \textsf{id}_{Q}$, done!

# Example of a new category $\textsf{Pointed}$:

- *(Objects.*) Rust types `A` with a chosen value `p:A`.
- *(Arrows.*) Arrows from `(A,p)` to `(B,q)`: Rust programs `fn f(a: A) -> B` such that `f(p) = q`.
- *(Identities.*) are exactly the usual ones in $\textsf{Prog}$.
- *(Composition.*) Composition is exactly the same as the one in $\textsf{Prog}$. We need to check that the composition satisfies the property we ask for arrows. Given three objects `(A,p)`, `(B,q)`, `(C,r)` and two programs `fn f(a: A) -> B`, `fn g(b: B) -> C`, we need to check that `g(f(p)) = r`. Since `f(p) = q`, this is the same as having to check that `g(q) = r`: but this is exactly what `g` satisfies, so we are done.

## Pointed has products, terminals, initials:

- Given two objects `(A,p)` and `(B,q)`, we pick the object
`(Pair<A,B>, Pair { first: p, second: q })`. Verify that this is indeed a product!
- The terminal object is `(Unit, Unit {})`, verify this!
- The initial object is also `(Unit, Unit {})`, verify this! Interesting claim.

## Note: it is NOT true in general that $A \times 0 \cong 0$.

In the category $\textsf{Prog}$, it is true.

In the category $\textsf{Pointed}$. Take for example the type `Bool`: then the product with $0$, which is defined `Unit`, is the type `Pair<Bool,Unit>`. This is not isomorphic to `Unit`! But it is isomorphic to `Bool`, again.

# EXERCISES: algebra of numbers

## Exercise 8.1

Show that $A \times 1 \cong A$.

## Exercise 8.2

Show that $A + 0 \cong A$.
