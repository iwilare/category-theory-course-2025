# 2025-02-28 - Lecture 6 (Exercises on products)

## Today's plan

- Small recap on the term language of a category.
- Get your hands dirty by building abstract arrows using only the definition of products.

# Example: the symmetry arrow

Take some category $C$ that you do not know anything about.
Assume the following things about $C$:
- There are two object $A,B$,
- There is an object $A \times B$ which is the product of $A$ and $B$.
- There is an object $B \times A$ which is the product of $B$ and $A$.

*Task objective:* obtain a arrow $\textsf{swap}_{A,B} : A \times B \to B \times A$.

*How?* Using the fact that we know that certain products exist.

...


```rust
fn swap(a: Pair<A,B>) -> Pair<B,A> {
    match a {
        Pair(v1,v2) => Pair(v2,v1)
    }
}
```

*Solution*: the desired arrow is $\langle \textsf{snd}  , \textsf{fst} \rangle : A \times B \to B \times A$.

In a single abstract step, we obtain useful information about all categories that we care about.

What does this tell us about the categories we know?

- In the category $\text{Prog}$ this tells us: there is a program `fn swap(a: Pair<A,B>) -> Pair<B,A>`.
- In the category $(\N, \le)$ this tells us: $\min\{a,b\} \le \min\{b,a\}$.
- In the category $(\N, \textsf{divides})$ this tells us: $\gcd\{a,b\} \textsf{ divides } \gcd\{b,a\}$.
- In the category $\text{BExpr}$ this tells us: if `A && B` is true then `B && A` is also true.

# Example: the associator arrow

Take some category $C$ that you do not know anything about.
Assume the following things about $C$:
- There are two object $A,B,C$,
- There is an object $A \times B$ which is the product of $A$ and $B$.
- There is an object $B \times C$ which is the product of $B$ and $C$.
- There is an object $(A \times B) \times C$ which is the product of $A \times B$ and $C$.
- There is an object $A \times (B \times C)$ which is the product of $A$ and $B \times C$.

*Task objective:* obtain a arrow $\textsf{assoc} : A \times (B \times C) \to (A \times B) \times C$.

*How?* Using the fact that we know that certain products exist.

...

```rust
fn assoc(v: Pair<A,Pair<B,C>>) -> Pair<Pair<A,B>,C> {
    match v {
        Pair(a, p) =>
            match p {
                Pair(b, c) => Pair(Pair(a, b), c)
            }
    }
}
fn assoc(v: Pair<A,Pair<B,C>>) -> Pair<Pair<A,B>,C> {
    Pair {
        first: v.fst(),
        second:
            Pair {
                first: v.snd().fst(),
                second: v.snd().snd()
            }
    }
}
fn assoc(v: Pair<A,Pair<B,C>>) -> Pair<Pair<A,B>,C> {
    Pair(Pair(v.fst(), v.snd().fst()), v.snd().snd())
}
```

<!--*Solution*: the desired arrow is $\langle \langle \textsf{fst} , \textsf{snd} \,; \textsf{fst} \rangle, \textsf{snd} \,; \textsf{snd} \rangle : A \times (B \times C) \to (A \times B) \times C$. -->

*Task objective 2*: Is there an opposite arrow? (with the other order of brackets), i.e., a arrow
$\textsf{antiassoc} : (A \times B) \times C \to A \times (B \times C)$?

<!-- *Solution*: the desired arrow is $\langle \textsf{fst} \,; \textsf{fst} , \langle \textsf{fst} \,; \textsf{snd} , \textsf{snd} \rangle \rangle : A \times (B \times C) \to (A \times B) \times C$. -->

# Example: the duplication arrow

Take some category $C$ that you do not know anything about.
Assume the following things about $C$:
- There are two object $A$,
- There is an object $A \times A$ which is the product of $A$ and $A$.

*Task objective:* obtain a arrow $\textsf{duplicate} : A \to A \times A$.

*How?* Using the fact that we know that certain products exist.

```rust
fn duplicate(v: A) -> Pair<A,A> {
    Pair(v, v)
}
```

<!--*Solution.* the desired arrow is $\langle \textsf{id}_A, \textsf{id}_A \rangle : A \to A \times A$.-->

# Example: the parallel arrow of $f$ and $g$

Show that given $f : A \to C$ and $g : B \to D$ then there is an arrow $A \times B \to C \times D$, which we denote as "$f \times g$" and that we call "$f$ in parallel with $g$".

*Solution.*

- Take $\quad \textsf{fst} \,; f : A \times B \to C,$
- Take $\quad \textsf{snd} \,; g : A \times B \to D,$

Assuming that the product $C \times D$ exists as an object of our category, we can take the pairing of the two above there is an arrow into the product $C \times D$:
$$
{\langle \textsf{fst} \,; f , \textsf{snd} \,; g \rangle} :A \times B \to C \times D
$$

# Example: show that $p \,; \langle l, r \rangle = \langle p \,; l , p \,; r \rangle$.

Abstract name of this property: *naturality of pairing.* We will see why in some lectures.

Take
- $l : H \to A$
- $r : H \to B$
- $p : X \to H$

And therefore $\langle l, r\rangle : H \to A \times B$.

We want to show that $p \,; \langle l, r \rangle = \langle p \,; l , p \,; r \rangle$.

> RECALL
>
> *(Pair expansion.*) for every object $H$ and arrow $h : H \to A \times B$:
> $$
>     \langle h \,; \textsf{fst} , h \,; \textsf{snd} \rangle = h.
> $$

Certainly, we can instantiate the pair expansion rule for $h = p \,; \langle l, r \rangle : X \to A \times B$.

Therefore:

$$
\begin{array}{lll}
  & p \,; \langle l, r \rangle & \text{(pair expansion for $h =p \,; \langle l, r \rangle$)} \\
= & \langle  p \,; \langle l, r \rangle \,; \textsf{fst} , \quad p \,; \langle l, r \rangle \,; \textsf{snd} \rangle & \text{(first pair equation)} \\
= & \langle  p \,; l , \hspace{4.3em} p \,; \langle l, r \rangle \,; \textsf{snd} \rangle & \text{(second pair equation)} \\
= & \langle  p \,; l , \hspace{4.3em} p \,; r \rangle. & \square
\end{array}
$$

# Example: show that $\langle \textsf{fst} , \textsf{snd} \rangle = \textsf{id}$

By pair expansion where $H = A \times B$ and $h = \textsf{id}_{A \times B} : A \times B \to A \times B$, we must have that
$$
\begin{array}{rlll}
    \langle \textsf{id} \,; \textsf{fst} , & \textsf{id} \,; \textsf{snd} \rangle & = \textsf{id}. \\
    \langle \textsf{fst} , & \textsf{snd} \rangle & = \textsf{id}.
\end{array}
$$



# Example: show that $\textsf{swap}_{A,B} \,; \textsf{swap}_{B,A} = \textsf{id}_{A \times B}$

*Intuition*: if you swap twice it's the same as just doing nothing.

We use the naturality of pairing property to aid ourselves in showing this.

$$
\begin{array}{lll}
  & \textsf{swap}_{A,B} \,; \textsf{swap}_{B,A} & \text{(definition)} \\
= & \langle \textsf{snd}_{A \times B} , \textsf{fst}_{A \times B} \rangle \,;  \langle \textsf{snd}_{B \times A} , \textsf{fst}_{B \times A} \rangle &  \\
= & \langle \textsf{snd} , \textsf{fst} \rangle \,;  \langle \textsf{snd} , \textsf{fst} \rangle & \text{(naturality of pairing for $p = \langle \textsf{snd} , \textsf{fst} \rangle$)} \\
= & \langle  \langle \textsf{snd} , \textsf{fst} \rangle \,; \textsf{snd} ,  \langle \textsf{snd} , \textsf{fst} \rangle \,; \textsf{fst} \rangle & \text{(pair equations)} \\
= & \langle \textsf{fst} , \hspace{4.9em} \textsf{snd} \rangle & \text{(example above)} \\
= & \textsf{id}. & \square
\end{array}
$$


# Intuition: products should be symmetric!

Take these two types:

```rust
enum Studentv1 {
    NotGraduated { name: String }
    Graduated { name: String, final_grade: i32 },
}

enum MaybeInteger {
    None,
    Some(i32),
}
struct Studentv2 {
    name: String,
    maybe_final_grade: MaybeInteger,
}
```

Why are these types "essentially" the same? Intuitively because I can construct two conversion functions that together compose to the identity (i.e., one is the inverse of the other):

```rust
fn v1tov2(a: Studentv1) -> Studentv2 {
    match a {
        NotGraduated { name } =>
            Studentv2 { name, maybe_final_grade: None },
        Graduated { name, final_grade } =>
            Studentv2 { name, maybe_final_grade: Some(final_grade) },
    }
}
fn v2tov1(a: Studentv2) -> Studentv1 {
    match a.maybe_final_grade {
        None => NotGraduated { name: a.name }
        Some(final_grade) => Graduated { name: a.name, maybe_final_grade: Some(final_grade)}
    }
}
```

We can reason by program equivalence to show that doing `v1tov2` and then `v2tov1` gets us exactly the original value back.

Assume to have `a: Studentv1`. There are only two possible ways in which this value `a` was created.
- Case 1. `a = NotGraduated { name: n }` for some string `n`:
  ```rust
    compose_v1tov2_v2tov1(a)
  = v2tov1(v1tov2(a))
  = v2tov1(match NotGraduated { name: n } {
        NotGraduated { name } =>
            Studentv2 { name, maybe_final_grade: None },
        Graduated { name, final_grade } =>
            Studentv2 { name, maybe_final_grade: Some(final_grade) },
    })
  = v2tov1(Studentv2 { n, maybe_final_grade: None })
  = match (Studentv2 { n, maybe_final_grade: None }).maybe_final_grade {
        None => NotGraduated { name: a.name }
        Some(final_grade) => Graduated { name: a.name, maybe_final_grade: Some(final_grade)}
    }
  = match None {
        None => NotGraduated { name: a.name }
        Some(final_grade) => Graduated { name: a.name, maybe_final_grade: Some(final_grade)}
    }
  = NotGraduated { name: a.name }
  = NotGraduated { name: (NotGraduated { name: n }).name }
  = NotGraduated { name: n }
  = a
  = id(a)
  ```
- Case 2. Follows more or less the same steps.

But look back at the process the just did: *we essentially established that `compose_v1tov2_v2tov1 = id`!* The same thing now must be done in the other direction, with approximately the same amount of pain.

## BOTH directions must be checked to capture this idea correctly.

```rust
// Just having two maps and not proving that they compose to the identity is not enough...

fn booltoint(b: bool) -> i32 {
    if b { 1 } else { 0 }
}
fn inttobool(a: i32) -> bool {
    a >= 1
}
```

We do have that if I have a boolean `b: bool`, then
- if `b = false` then it's sent to `0`, which is then sent back to `false`.
- if `b = true` then it's sent to `1`, which is then sent back to `true`.

But of course, in the other direction, the number `7` is sent to `true` and then to `1`, which is not the number I started with.


### To connect it back to the abstract case...

Not only there are maps

$\textsf{swap} : A \times B \to B \times A$,

$\textsf{assoc} : A \times (B \times C) \to (A \times B) \times C$,

$\textsf{antiassoc} : (A \times B) \times C \to A \times (B \times C)$

but, intuitively, we want to say that the two objects really should be thought of "as the same"!

- In $\text{Prog}$, for example, these two types are essentially the same in the sense that we described above:
    ```rust
    struct Pairv1 {
        first: A
        second: B
    }

    struct Pairv2 {
        fgh: B,
        asd: A,
    }
    ```
    even though they are not literally the same type.

- In $(\N,\le)$ something even stronger happens: the product in $(\N,\le)$ of two numbers $n$, $m$ is literally the same number as the product of $m,n$, since, intuitively, we expect that $\min\{a,b\} = \min\{b,a\}$ (although we haven't yet shown this once and for all!).

All of these considerations lead to the following definition:

# Definition: isomorphisms

Two objects $A$ and $B$ are said to be *isomorphic*, which we write as $A \cong B$, whenever the following thing happens:

You provide two arrows
- $\textsf{a2b} : A \to B,$
- $\textsf{b2a} : B \to A,$

such that the following two equations hold:

$$\textsf{a2b} \,; \textsf{b2a} = \textsf{id}_A, \quad \textsf{b2a} \,; \textsf{a2b} = \textsf{id}_B.$$

# EXERCISES

### Exercise 6.1: being isomorphic is a reflexive notion.
For any object $A$, show that $A \cong A$.
### Exercise 6.2: being isomorphic is a symmetric notion.
For any object $A,B$, show that if I know that $A \cong B$, then $B \cong A$.
### Exercise 6.3: being isomorphic is a transitive notion.
For any object $A,B,C$, show that if I know that $A \cong B$ and that $B \cong C$, then I know that $A \cong C$.
### Exercise 6.4
Show that $A \times B \cong B \times A$.
### Exercise 6.5
Show that $A \times (B \times C) \cong (A \times B) \times C$.
### Exercise 6.6
Show that given $f : A \to C$ and $g : B \to D$ then there is an arrow $f \times g : A \times B \to C \times D$.
### Exercise 6.7
Among the examples that we have seen in the previous lecture, find a category where $A \not \cong A \times A$ and one where $A \cong A \times A$.
