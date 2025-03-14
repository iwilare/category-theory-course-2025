# 2025-02-27 - Lecture 5 (Products)

## Today's plan

This lecture marks the end of introductory/preliminary material for category theory. From this lecture onwards, the real category theory begins, and we can start applying the power of categorical reasoning to show general properties of categories "for free".

- Introduce other important examples of categories (the preorder of natural numbers under two, the preorder of deductions),
- Define the notion of product.

Here are three more examples of categories (preorders, actually) that we will use today for examples of products.

# Example of category: the preorder $(\N,\le)$ of natural numbers using $\le$

- *(Objects.)* The type of natural numbers $\N$.
- *(Arrows.)* There is exactly one arrow from $n$ to $m$ when $n \le m$.
$$
0 \to 1 \to 2 \to \cdots
$$
- *(Identities.)* yes, because for every $n:\textsf{Nat}$, we have that $n \le n$.
- *(Composition.)* yes, because for every $n,m,k:\textsf{Nat}$, we have that, whenever $n \le m$ and $m \le k$, then $n \le k$.

# Example of category: the preorder $(\N,\textsf{divides})$ of natural numbers using $\textsf{divides}$

- *(Objects.)* The type of natural numbers $\N$.
- *(Arrows.)* There is exactly one arrow from $n$ to $m$ when $n$ divides $m$.

    Formally: $n$ divides $m$ exactly when there exists $k:\N$ such that $m = k \cdot n$.

    For example: $3 \textsf{ divides } 9$, $2 \textsf{ divides } 12$, $10 \textsf{ divides } 10$, but $!(3 \textsf{ divides } 7)$, $!(4 \textsf{ divides } 2)$.
- *(Identities.)* yes, because for every $n:\textsf{Nat}$, we have that $n \textsf{ divides } n$. (in this case $k=1$)
- *(Composition.)* yes, because for every $n,m,r:\textsf{Nat}$, we have that, whenever $n \textsf{ divides } m$ and $m \textsf{ divides } r$, then $n \textsf{ divides } r$.
Formally:
    - suppose $m = n \cdot k_1$ for some $k_1$,
    - suppose $r = m \cdot k_2$ for some $k_2$,
    - I need to find $k_3$ such that $r = n \cdot k_3$. I simply pick $k_3 = k_1 \cdot k_2$.
    This works because $r = m \cdot k_2 = (n \cdot k_1) \cdot k_2 = n \cdot (k_1 \cdot k_2) = n \cdot k_3$.

# Example of category: the preorder $(\text{BExpr}, \to)$ of boolean propositions and implications between them

Let's work with the type of boolean expressions, i.e., expressions that I can put as the condition of an if-then-else statements.

```rust
enum BExpr {
    LessEq(String, i32),
    GreaterEq(String, i32),
    And(BExpr, BExpr),
    Or(BExpr, BExpr),
    Not(BExpr),
    F,
    T,
}
```
Some examples:
- `x ≤ 7` (i.e., formally, `LessEq("x", 7)`).
- `!(y ≤ 3 && x ≥ 4)` (i.e., formally, `Not(And(LessEq("y", 3), GreaterEq("x", 4)))`).
- `!(x ≥ 0 || false)` (i.e., formally, `Not(Or(GreaterEq("x", 0), F))`).

We define the following category $\textsf{BExpr}$.

- *(Objects.)* The type `BExpr`.
- *(Arrows.)* There is exactly one arrow from `P` to `Q` whenever, knowing that `P` is true, I also know that `Q` is true.

    Examples:
    - `x ≤ 7` $\to$ `x ≤ 10`
    - `x ≤ 7 && x ≤ 4` $\to$ `x ≤ 5`
    - `!(x ≤ 7)` $\to$ `x ≥ 8`
    - `x ≤ 7 || false` $\to$ `x ≤ 7`

     In other words, there is an arrow whenever `P` implies `Q`.
     (In other words, there is an arrow whenever `!P || Q` is true.)

     Yet in other words, this implication operator between `P` and `Q` is true according to the following truth table:

     | P | Q | P $\to$ Q |
     |--|--|--|
     0 | 0 | 1
     0 | 1 | 1
     1 | 0 | 0
     1 | 1 | 1

- *(Identities.)* yes, because if I know that `P` is true then `P` is also true.
- *(Composition.)* yes, because for every `P,Q,R:BExpr`:
    1. imagine I know that `P` implies `Q`.
    2. imagine I know that `Q` implies `R`.

    Now, assume I know that `P` is true. Then, by (1.), I know that `Q` is true. But then, by (2.) I know that `R` is true. Therefore, I managed to deduce that `R` is true from the fact that `P` is true.

# Definition: product of two objects $A$, $B$

Fix two objects $A,B$ in a category. These will stay fixed for the rest of the definition.

---

An object $P$ is said to be *a product of $A$ and $B$* if the following conditions are satisfied:

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A,$
    - $\textsf{snd} : P \to B,$
2. *(Existence of pairs and pair equations.)* if someone gives you the following data,
    - another object $H$,
    - an arrow $l : H \to A,$ and
    - an arrow $r : H \to B,$

    then you must pick an arrow
    - $p : H \to P$,

    such that these equations hold, which we will call the ***Pair equations***:
    - $p \,; \textsf{fst} = l$,
    - $p \,; \textsf{snd} = r$.

    Since the choice of $p$ depends on the $f$ and $g$ that you gave me, we will instead write the arrow $p$ that *you* must pick as
    $$\langle l , r \rangle : H \to P.$$
    A less-mathy notation for this is to instead denote the map above as $\textsf{pairing}_{l,r} : H \to P.$

    With this notation, the equations above are these:
    - $\langle l , r \rangle \,; \textsf{fst} = l$,
    - $\langle l , r \rangle \,; \textsf{snd} = r$.

3. *(Pair expansion.)* the choice for $\langle \cdots , \cdots \rangle$ that you gave above must satisfy the following equation for every object $H$ and every arrow $h : H \to P$:
$$
    \langle h \,; \textsf{fst} , h \,; \textsf{snd} \rangle = h.
$$

**(End of definition.)**

There are other equivalent formulations for products that you might find in the wild, they are all equivalent.

> We will sometimes write $A \times B$ to indicate an object $P$ that satisfies the property of being a **product** of some other two objects $A,B$.

## Products sometimes might not exist:

In the category with two objects and only identities, the product of `A` and `B` does not exist (since there are no morphisms that connect `A` and `B`!)

```
   A           B
   .           .
```

## Examples of products: $\text{Prog}$

1. The category $\text{Prog}$ of Rust types and total programs between them.

Remember that composition of programs is given by the program
```rust
fn compose_f_g(a: A) -> C {
    g(f(a))
}

// Equivalently, we will use method chaining so that the two functions are in the same order as in the expression $f ; g$:

fn compose_f_g(a: A) -> C {
    a.f().g()
}
```

Moreover, remember that we will consider equivalence of programs as *behavioural equivalence*.

**For the rest of this lecture, we consider Rust with these restrictions:**

- `i32`, `String`, `bool` types
- Functions only take one argument, and as always never loop/crash, etc. and always terminate (this is what total means.)
- The `Pair<A,B>` type for any two types `A`, `B`, defined like this:
    ```rust
    struct Pair<A,B> {
        first: A,
        second: B,
    }
    ```
(In particular, these are also types: `Pair<i32,Pair<String,i32>>`, `Pair<Pair<String,String>,bool>`, etc...!)

## Let's show that $\text{Prog}$ has all products

Recall that `Prog` has as objects all the types we just listed and all total programs between as arrows.

What does it mean to have "all products"? For any `A,B` types, I will give you a choice `P` of a type, and I'm going to tell you that this `P` that I chose really is a product.

*Proof.*

- *(Choice of $P$.)* For any two types `A`, `B`, we say that there is an object which satisfies the property of being a product of `A`, `B`. The object we select for $P$ is `Pair<A,B>`.

> RECALL:
> 1. *(Existence of projections.)* you must pick two arrows
>     - $\textsf{fst} : P \to A,$
>     - $\textsf{snd} : P \to B,$

- *(Existence of projections.)* We need to provide two arrows $\texttt{fst}: \texttt{Pair<A,B>} \to A, \texttt{snd}: \texttt{Pair<A,B>} \to \texttt B$, i.e., construct certain Rust programs with the given signature. We define them like this:

    ```rust
    fn fst(p: Pair<A,B>) -> A {
        match p {
            Pair(first, second) => first
        }
        // Alternatively: p.first
    }

    fn snd(p: Pair<A,B>) -> B {
        match p {
            Pair(first, second) => second
        }
        // Alternatively: p.second
    }
    ```

> RECALL:
>
> 2. *(Existence of pairs.)* if someone gives you the following data,
>     - another object $H$,
>     - an arrow $l : H \to A,$ and
>     - an arrow $r : H \to B,$
>
>     then you must pick an arrow
>     - $\langle l , r \rangle  : H \to P$,
>
>     such that these equations hold:
>     - $\langle l , r \rangle  \,; \textsf{fst} = l$,
>     - $\langle l , r \rangle  \,; \textsf{snd} = r$.

- *(Existence of pairing.)* Assume someone comes up to us and gives us a type `H` and two programs

    ```rust
    fn l(v: H) -> A => ...
    fn r(v: H) -> B => ...
    ```

    The pairing  arrow `pairing_l_r` for `l` and `r` is defined as follows:

    ```rust
    fn pairing_l_r(v: H) -> Pair<A,B> {
        Pair(l(v), r(v))
        // equivalently: Pair { first: l(v), second: r(v) }
    }
    ```

    Let's verify the equations $\lang l,r \rang\,;\textsf{fst} = l$, $\lang l,r \rang\,;\textsf{snd} = r$.
    Remember that composition is "substitution" of one definition into the other, and that equality is program equivalence.

    - $\lang l,r \rang\,;\textsf{fst} = l$

        The first arrow is this program:
        ```rust
            fn compose_(pairing_l_r)_fst(v: H) -> A {
                v.pairing_l_r().fst()
            }
        ```
        Let's reason by program equivalence, for some argument `v: H`:
        ```rust
            v.pairing_l_r().fst()          // (Function expansion.)
            = Pair(l(v), r(v)).fst()       // (Function expansion.)
            = (match Pair(l(v),r(v)) {
                 Pair(a,b) => a
              })              // (Match evaluation.)
            = l(v)
        ```
        which is exactly the definition of ```fn l(v: H) -> A```.

    - $\lang l,r \rang\,;\textsf{snd} = r$

        The first arrow is this program:
        ```rust
            fn compose_(pairing_l_r)_snd(v: H) -> A =>
            v.pairing_l_r().snd()
        ```
        Let's reason by program equivalence, for some argument `v: H`:
        ```rust
            v.pairing_l_r().snd()          // (Function expansion.)
            = Pair(l(v), r(v)).snd()       // (Function expansion.)
            = (match Pair(l(v),r(v)) {
                Pair(a,b) => b
              })              // (Match evaluation.)
            = r(v)
        ```
        which is exactly the definition of ```fn r(v: H) -> A```.

> RECALL:
>
>   *(Pair expansion.)* the choice for $\langle \cdots , \cdots \rangle$ that you gave above must satisfy the equation $\langle h \,; \textsf{fst} , h \,; \textsf{snd} \rangle = h$ holds for every arrow $h : H \to P$.

- *(Pair expansion)*

    Assume to have a arrow `fn p(v: H) -> Pair<A,B>` which also satisfies the *product equalities*. We show that is program equivalent to `pairing_(compose_p_fst)_(compose_p_snd)`.

    Assume to have a value `v: H`.
    and let's assume that when we call `p` on this value we get a result `Pair(v1,v2)` for some values `v1: A` and `v2: B`.
    ```rust
           v.p()
         = Pair(v1, v2)        // (General principle.)
                               // Assumption that the result of this function is given
                               // by combining some values `v1`, `v2`.
    ```
    ```rust
           v.pairing_(compose_p_fst)_(compose_p_snd)  // (Function unfolding.)
         = Pair(v.compose_p_fst(), v.compose_p_snd()) // (Function unfolding.)
         = Pair(v.p().fst(), v.p().snd())             // (Assumption.)
         = Pair(Pair(v1,v2).fst(), Pair(v1,v2).snd()) // (Function unfolding.)
         = Pair(match Pair(v1,v2) {
                    Pair(a,b) => a
                },
                match Pair(v1,v2) {
                    Pair(a,b) => b
                })                     // (Match evaluation.)
         = Pair(v1, v2)
    ```

# Products in a category corresponds to pairs in a programming language.

Take the **term language** of some category $C$.

> *Claim*: If you tell me that $C$ has a product $A \times B$ for every object $A,B$, then,
> the term language of $C$ is extended to have a type `Pair<A,B>` for every type `A,B`, a pair constructor `Pair { first: ..., second: ... } `,  and `match` constructs, that behave in the correct way.
> - Moreover, every program that can be created in this language corresponds with the arrows of the category $C$.
> - Moreover, every program equivalence that can be shown in this language corresponds exactly with the equalities of the category $C$.

```rust
struct Pair<A,B> {
    first: A,
    second: B
}
```

These choices are "well-behaved" because of the properties of products.

We sketch the above idea that allows you to translate between programs and arrows, (and viceversa!):

- > *Being able to construct elements of a pair is the same as the existence of the pairing arrow.*

    Informally:

    ```rust
    Pair(<expr1>[x], <expr2>[x])
    ```

    can be replaced by

    ```rust
    pairing_expr1_expr2[x]
    ```
    (we consider `expr1` and `expr2` as "functions" which take their free variables as arguments.)

- > *Being able to `match` is the same as having `fst` and `snd` as functions of our language.*

    ```rust
    match <expr> {
        Pair(a,b) => <body>[a,b]
    }
    ```
    becomes
    ```rust
    <body>[<expr>.fst(),<expr>.snd()]
    ```

    For example:

    ```rust
    fn isAdultWithNameLength(p: Pair<i32, String>) -> Pair<bool, i32> {
        match process_data(p) {
            Pair(age,name) => Pair(age >= 18, name.length())
        }
    }
    ```

    is program equivalent to

    ```rust
    fn isAdultWithNameLength(p: Pair<i32, String>) -> Pair<bool, i32> {
        Pair(process_data(p).fst() >= 18, process_data(p).snd().length())
    }
    ```
- > The product equalities of the pairing arrow say how the `fst` and `snd` functions behave on pairs (i.e., they give you exactly the argument that you ask for), and allows us to use the *(Match evaluation)* rule when running a program and in our program equalities. (Remember that `fst` and `snd` were defined by match evaluation.)
- > The pair expansion property for the pairing arrow says that pairs are determined by their `fst` and `snd`, and nothing else:
    *If I take a pair $p$, destructure it into its two components and then recombine it back using pairs it's just the same as giving the pair directly.*

    The pair expansion equation translates to the fact that, intuitively, for every `p: Pair<A,B>`:

    ```rust
    Pair(p.first, p.second) = p
    ```

     **(this is not precise! In category theory we talk about arrows, not about ""elements of a type"", which does not make any sense!! Objects of a category are just symbols, they are not types, in general.)**

    Similarly, using `match`, this expression is just the same as `p`:

    ```rust
    match p {
        Pair(a,b) => Pair(a,b)
    }
    ```

# Why are they called products?

Consider the category $\text{FinProg}$, which is just the ""sub-category"" (we have not defined this yet precisely) where the types are only the finite ones.

```rust
// Recall: general definition of the product, for any types `A`, `B`
struct Pair<A,B> {
    first: A,
    second: B
}
```

```rust
// Consider these two types:
enum Two {
    A1, A2
}

enum Three {
    B1, B2, B3
}

// Why are products called like this?
// How many possible ways are there to construct elements of this type?
struct Pair_Two_Three {
    first: Two,
    second: Three,
}

// There are all possibilities:

// Pair_Two_Three { first: A1, second: B1 }
// Pair_Two_Three { first: A1, second: B2 }
// Pair_Two_Three { first: A1, second: B3 }

// Pair_Two_Three { first: A2, second: B1 }
// Pair_Two_Three { first: A2, second: B2 }
// Pair_Two_Three { first: A2, second: B3 }
```

As you can see,
$\textsf{size}(\texttt{Two}) = 2$,
$\textsf{size}(\texttt{Three}) = 3$,
$\textsf{size}(\texttt{Pair\_Two\_Three}) = 2*3 = 6$.

In general, given two types `A,B` such that
- $\textsf{size}(A) = n$, and
- $\textsf{size}(B) = m$, then
- $\textsf{size}(\texttt{Pair}(A,B)) = n * m$.

Now, the product of numbers (i.e., the size of types) is associative, commutative, etc... we will return back to this in the next lecture.

# Examples of products: $(\N, \le)$

Idea: a "product" of the two numbers $A$ and $B$ always exists, and it's the number $\min\{A,B\}$.

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A, \qquad \min\{A,B\} \le A,$
    - $\textsf{snd} : P \to B, \qquad \min\{A,B\} \le B,$
2. *(Existence of pairs.)* if someone gives you the following data,
    - another object (number) $H$,
    - two arrows $l : H \to A, \quad$ i.e., I know that $H \le A,$
    - two arrows $r : H \to B, \quad$ i.e., I know that $H \le B,$

    then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that $H \le \min\{A,B\}.$

    ~~such that these equations hold:~~ (this does not need to be checked since we are in a preorder!)
    - ...

**In other words, $\min\{A,B\}$ is the "largest" number that is smaller than both $A$ and $B$.**

*A choice that unfortunately is not a product:* given two numbers $a$, $b$, I pick as their product the number $1$. Projections exist but the existence of pairs condition fails.

# Examples of products: $(\N, \textsf{divides})$

Idea: a "product" of two numbers $A$ and $B$ always exists, and it's the number $\gcd\{A,B\}$.

Example: $A = 28, B = 12$, which are just $7*4$ and $3*4$.

Then, $\gcd\{A,B\} = 4$. (Note! $2$ also divides both of them, but it's not the smallest divisor.)

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A, \qquad \gcd\{A,B\} \textsf{ divides } A,$
    - $\textsf{snd} : P \to B, \qquad \gcd\{A,B\} \textsf{ divides } B,$
2. *(Existence of pairs.)* if someone gives you the following data,
    - another object (number) $H$,
    - two arrows $l : H \to A, \quad$ i.e., I know that $H \textsf{ divides } A,$
    - two arrows $r : H \to B, \quad$ i.e., I know that $H \textsf{ divides } B,$

    then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that $H \textsf{ divides } \gcd\{A,B\}.$

    ~~such that these equations hold:~~ (this does not need to be checked since we are in a preorder!)
    - ...

**In other words, $\gcd\{A,B\}$ is the "largest" divisor that divides both $A$ and $B$.**

# Examples of products: $(\text{BExpr}, \to)$

Idea: a "product" of two propositions `A` and `B` always exists, and it's the proposition `A && B`.

| A | B | A && B |
|--|--|--|
0 | 0 | 0
0 | 1 | 0
1 | 0 | 0
1 | 1 | 1

1. *(Existence of projections.)* you must pick two arrows
    - $\textsf{fst} : P \to A, \qquad$ the implication `A && B` $\to$ `A`
    - $\textsf{snd} : P \to B, \qquad$ the implication `A && B` $\to$ `B`

    Intuitively, this works! If `A && B` is true then certainly `A` is true.

2. *(Existence of pairs.)* if someone gives you the following data,
    - another object (proposition) `H`,
    - two arrows $l : H \to A, \qquad$ the implication `H` $\to$ `A`,
    - two arrows $r : H \to B, \qquad$ the implication `H` $\to$ `B`,

    then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that `H` $\to$ `A && B`.

    ~~such that these equations hold:~~ (this does not need to be checked since we are in a preorder!)
    - ...

---

# Products are not unique

For any given category, there are many possible equivalent choices for products, they are not unique!
All of these choices for $P$ satisfy the property of being a product (you can check this by yourself as exercise!):

```rust
enum Pair<A,B> {
    first: A,
    second: B,
}
enum EquivalentPair1<A,B> {
    abc: B,
    def: A,
}

struct NoFields {

}
enum EquivalentPair2<A,B> {
    abc: B,
    def: A,
    extra: NoFields, // This was introduced in Lecture 1, we will se it again next lecture.
}
```

In the other examples, `A && B && true` also satisfies the property of being a product.

As you can see, however, all of these are intuitively "equivalent", in some sense: e.g., they contain the same data, or, in the case of predicates, one predicate is true exactly when the other one is true and viceversa. We will also make this precise in the next lecture.

# Intuition: Products = correctness (arrows) + no garbage (pairing) + uniqueness (pair expansion)

- The first condition *existence of projection* can be thought of as "correctness" of pairs, i.e., we truly do have two projection functions out of a pair of things.

If this was the only condition, however, many other choices of product would apply.
In the example of $\text{Prog}$, for example, there are projection maps also for this choice of type (instead of `Pair<A,B>`):

```rust
enum AlmostPair<A,B> {
    first: A,
    second: B,
    extra: i32
}
```

Certainly this choice gives us the projection arrows/programs into `A` and `B`.
For the other examples, `A && B && x == 0` also gives us projection maps, but intuitively it gives us something "more" than just the conjunction.

- The second condition *existence of pairing* can be thought of as the fact that pairs are not allowed to have any "extra" information than the one that they already have.
**NOTE!** This is never captured at the level of *objects*, but at the level of *arrows*, by asking that certain arrows exist. (Does "8" know that it's the minimum of 10 and 8? No! This "being the minumum fact" is determined by the fact that certain arrows (i.e., $\le$ relations) are true.)
Objects in a category are just the values of a certain Rust type, so they are just symbols.

Let's see why the above choices fail because of the second condition:

For $\textsf{Prog}$:

2. *(Existence of pairs/no garbage.)* if someone gives you the following data,
    - another object (proposition) `H`,
    - two arrows $l : H \to A, \qquad$ the implication `H` $\to$ `A`,
    - two arrows $r : H \to B, \qquad$ the implication `H` $\to$ `B`,
    then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that `H` $\to$ `A && B && x == 0`.


For $\textsf{BExpr}$:

2. *(Existence of pairs/no garbage.)* if someone gives you the following data,
    - another object (proposition) `H`,
    - two arrows $l : H \to A, \qquad$ the implication `H` $\to$ `A`,
    - two arrows $r : H \to B, \qquad$ the implication `H` $\to$ `B`,
then you must pick an arrow
    - $p : H \to P$,  $\quad$ i.e., I must show that `H` $\to$ `A && B && x == 0`.

Of course this cannot be done!
Maybe `H` explicitly states that `x != 0`, for example, if `H = A && B && x == 4` then we have the two implications at the top, but not the one at the bottom.

Therefore, the real choice of product must be `A && B`, i.e., it must the predicate that contains *only* the necessary information and nothing else.

> # (\*) The first important idea in category theory: the general concept of category and product allows you to find connections between things that were not connected before. (`Pair` and $\min$ and $\gcd$ and `&&`)
