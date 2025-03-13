# 2025-03-13 - Lecture 8 (Initial, terminal, exponential objects)

## Today's plan

- Define initial and terminal objects
- Explore examples in our categories

# Definition: initial object

An object $A$ of a category $C$ is called an *initial object* if the following condition holds:

1. *(Existence of arrows to every object.)* For every object $H$ in $C$, there is an arrow $!_H: A \to H$.
2. *(Uniqueness of this arrow.)* for every object $H$, any arrow $h : A \to H$ is actually equal to $h = {!_H}$.

```

   .        .        .      .    .
   H1       H2       H3     A    H5

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

<!--it would have to do something different for at least one input value. But since there are no values of type `Empty`, there can be no behavioral difference between any two functions of this signature.-->

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

# Exponential objects

In the previous lecture, we saw how products gave us a way to "pair" two objects. Today, we'll see how we can represent "functions" between objects categorically.

### Definition: exponential object $B^A$

Fix two objects $A, B$ in a category with products. These will stay fixed for the rest of the definition.

---

An object $E$ is said to be *an exponential object from $A$ to $B$* (notation: $B^A$) if the following conditions are satisfied:

1. *(Existence of evaluation.)* you must pick an arrow
   - $\textsf{eval} : E \times A \to B$

2. *(Existence of lambda abstraction.)* if someone gives you the following data,
   - another object $H$,
   - an arrow $f : H \times A \to B$,

   then you must pick an arrow
   - $\Lambda(f) : H \to E$,

   such that this equation holds, which we will call the *Lambda equation*:
   - $\langle \Lambda(f) \times \textsf{id}_A \rangle \,; \textsf{eval} = f$

   Here, $\Lambda(f) \times \textsf{id}_A : H \times A \to E \times A$ is the arrow that applies $\Lambda(f)$ to the first component of the pair and leaves the second component unchanged.

3. *(Eta uniqueness.)* the choice for $\Lambda$ that you gave above must satisfy the following equation, for every object $H$ and every arrow $h : H \to E$:

    $$\Lambda(\langle h \times \textsf{id}_A \rangle \,; \textsf{eval}) = h$$

    This property is called eta-expansion because it corresponds to the eta-equivalence rule in lambda calculus, which states that any function $f$ is equivalent to $\Lambda x. f(x)$. In categorical terms, if we take a function $h$ and apply it to an argument and then abstract over that argument again, we get back the original function.

> *Claim.* The choice for $\Lambda$ that you gave above must be unique. That is, for every object $H$ and arrow $f : H \times A \to B$, if there is another arrow $g : H \to E$ that satisfies the lambda equation, then $g = \Lambda(f)$.

**(End of definition.)**

Intuitively, the exponential object $B^A$ represents "the space of all functions from $A$ to $B$". The evaluation arrow lets us apply a function to an argument, and lambda abstraction lets us "curry" a function of two arguments into a function that returns another function.

## Examples of Exponential Objects: $\text{Prog}$

Let's show that the category $\text{Prog}$ has all exponential objects. Remember that $\text{Prog}$ has Rust types as objects and total programs as arrows.

*Proof.*

- *(Choice of $E$.)* For any two types `A`, `B`, we say that there is an object which satisfies the property of being an exponential object from `A` to `B`. The object we select for $E$ is `fn(A) -> B`, the function type from `A` to `B`.

- *(Existence of evaluation.)* We need to provide an arrow $\textsf{eval}: \textsf{fn(A) -> B} \times \textsf{A} \to \textsf{B}$, i.e., construct a Rust program with the given signature. We define it like this:

    ```rust
    fn eval(p: Pair<fn(A) -> B, A>) -> B {
        (p.first)(p.second)
    }
    ```

- *(Existence of lambda abstraction.)* Assume someone gives us a type `H` and a program:

    ```rust
    fn f(p: Pair<H, A>) -> B => ...
    ```

    The lambda abstraction arrow `lambda_f` for `f` is defined as follows:

    ```rust
    fn lambda_f(v: H) -> Func<A,B> {
        |a: A| f(Pair(v, a))
    }
    ```

    Let's verify the lambda equation:
    $\langle \Lambda(f) \times \textsf{id}_A \rangle \,; \textsf{eval} = f$

    The composed arrow is this program:
        ```rust
        fn compose_lambda_f_eval(p: Pair<H, A>) -> B {
            eval(Pair(lambda_f(p.first), p.second))
        }
        ```

    Let's reason by program equivalence, for some argument `p: Pair<H, A>`:
    ```rust
    eval(Pair(lambda_f(p.first), p.second))  // (Function expansion)
    = eval(Pair(|a: A| f(Pair(p.first, a)), p.second))  // (Function expansion)
    = (|a: A| f(Pair(p.first, a)))(p.second)  // (Function application)
    = f(Pair(p.first, p.second))  // (Pair expansion)
    = f(p)
    ```

    - *(Eta uniqueness.)* Assume we have a program `h: H -> Func<A,B>`, we show that $h$ is program equivalent to $h = \Lambda(\langle h \times \textsf{id}_A \rangle \,; \textsf{eval})$.
    The second program is the following, for some value `v:H`:

    ```rust
          v.lambda_(compose_(pairing_h_id)_eval)
        = |a: A| compose_(pairing_h_id)_eval(Pair(v, a))
        = |a: A| Pair(h(v), id(a)).eval()
        = |a: A| Pair(h(v), a).eval()
        = |a: A| h(v)(a)
        = h(v)
    ```

    Since the functions agree on all inputs, they are equivalent.

## Exponential Objects represent Function Types

Just as products in a category correspond to pairs in a programming language, exponential objects correspond to function types.

If you tell me that a category $C$ has all products and all exponential objects, then:
- The term language of $C$ is extended to have function types `fn(A) -> B` for every type `A`, `B`
- Function application `f(x)` corresponds to the evaluation arrow
- Lambda abstractions `|x| e` correspond to the lambda abstraction arrow
- The beta-reduction rule in lambda calculus corresponds to the lambda equation

Consider the translation:

```rust
// Lambda abstraction
|x: A| e[x]
```

can be replaced by

```rust
lambda_expr[free variables]
```

The lambda equation is precisely the beta-reduction rule from lambda calculus:
```
(|x| e)(a) = e[a/x]
```

## Examples of Exponential Objects: $(\N, \le)$

Recall that in $(\N, \le)$, the product of two numbers $A$ and $B$ is $\min\{A, B\}$.

Question: Does this category have exponential objects?

Let's think about what an exponential object $B^A$ would mean in this context:

1. It needs to be a number $E$ such that there's an arrow $\textsf{eval} : E \times A \to B$
2. For any $H$ with an arrow $f : H \times A \to B$, there must be an arrow $\Lambda f : H \to E$

Since this is a preorder, arrows are just the $\le$ relation, and the exponential object corresponds to "implication" in this order.

In $(\N, \le)$, we need to find a number $E$ such that:
- $\min(E, A) \le B$
- For any $H$ where $\min(H, A) \le B$, we have $H \le E$

$E$ must be the "largest" number such that $\min(E, A) \le B$.

If $A \le B$, then we can take $E = \infty$ (or a sufficiently large number).
If $A > B$, there's no valid choice for $E$.

Therefore, exponential objects don't always exist in $(\N, \le)$.

## Examples of Exponential Objects: $(\text{BExpr}, \to)$

In boolean logic, the product corresponds to conjunction (AND). The exponential object corresponds to logical implication.

For propositions $A$ and $B$, the exponential object $B^A$ is $A \Rightarrow B$ (read: "A implies B").

1. *(Existence of evaluation.)* The arrow $\textsf{eval} : (A \Rightarrow B) \times A \to B$ corresponds to modus ponens:
   - If I know "A implies B" and I know "A", then I can deduce "B"

2. *(Existence of lambda abstraction.)* If for some proposition $H$, I know that $H \land A$ implies $B$, then $H$ implies $(A \Rightarrow B)$

The truth table for implication is:

| A | B | A ⇒ B |
|---|---|-------|
| 0 | 0 |   1   |
| 0 | 1 |   1   |
| 1 | 0 |   0   |
| 1 | 1 |   1   |

## Cartesian Closed Categories (CCCs)

A category is called *Cartesian closed* if:
1. It has a terminal object (we'll define this next lecture)
2. It has all binary products
3. It has all exponential objects

Cartesian closed categories are particularly important in computer science because they provide a mathematical foundation for typed lambda calculi and functional programming languages. In particular:

- Objects correspond to types
- Arrows correspond to functions
- Products correspond to pair types
- Exponential objects correspond to function types

The category $\text{Prog}$ of Rust types and total programs is Cartesian closed, as we've shown that it has all products and exponential objects.

## Why are they called exponential objects?

In $\text{FinProg}$, the subcategory with finite types:

```rust
// Consider these two types:
enum Three {
    A1, A2, A3
}

enum Two {
    B1, B2
}

enum TwoToThree = Func<Two,Three>

// 3^2 = 9
// |a| match a { B1 => A1, B2 => A1 }
// |a| match a { B1 => A1, B2 => A2 }
// |a| match a { B1 => A1, B2 => A3 }
// |a| match a { B1 => A2, B2 => A1 }
// |a| match a { B1 => A2, B2 => A2 }
// |a| match a { B1 => A2, B2 => A3 }
// |a| match a { B1 => A3, B2 => A1 }
// |a| match a { B1 => A3, B2 => A2 }
// |a| match a { B1 => A3, B2 => A3 }

enum ThreeToTwo = Func<Three,Two>

// 2^3 = 8
// |a| match a { A1 => B1, A2 => B1, A3 => B1 }
// |a| match a { A1 => B1, A2 => B1, A3 => B2 }
// |a| match a { A1 => B1, A2 => B2, A3 => B1 }
// |a| match a { A1 => B1, A2 => B2, A3 => B2 }
// |a| match a { A1 => B2, A2 => B1, A3 => B1 }
// |a| match a { A1 => B2, A2 => B1, A3 => B2 }
// |a| match a { A1 => B2, A2 => B2, A3 => B1 }
// |a| match a { A1 => B2, A2 => B2, A3 => B2 }
```

If $\textsf{size}(A) = n$ and $\textsf{size}(B) = m$, then:
- $\textsf{size}(B^A) = m^n$

For example, if $A$ has 2 elements and $B$ has 3 elements, then the number of possible functions from $A$ to $B$ is $3^2 = 9$.

This is exactly the number of elements in the function type `fn(A) -> B`.
