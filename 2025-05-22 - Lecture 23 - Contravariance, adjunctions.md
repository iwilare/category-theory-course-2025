# 2025-05-22 - Lecture 23 (Contravariance, adjunctions)

# Adjunctions

*(Definition.)* Take two categories $C$ and $D$ with two functors $L : C \to D$ and $R : D \to C$.
We say that $L$ is left adjoint to $R$, or equivalently, that $R$ is right adjoint to $L$, if for every $X$ in $C$ and $Y$ in $D$ there is an isomorphism in $\textsf{Prog}$ between these two types of arrows: for every $X$ in $D$ and every $Y$ in $C$ there is an isomorphism between "arrows"

$$\begin{array}{ccc}
L(X) & \longrightarrow & \phantom{R(}Y\phantom{)} & \text{in }D\\ \hline \hline
\phantom{L(}X\phantom{)} & \longrightarrow & R(Y) & \text{in }C\\
\end{array}
$$

The two types being isomorphic are `ArrD_(L_obj(X))_Y` and `ArrC_X_(R_obj(Y))`, for every object `X` and `Y`.

*Moreover, the two programs witness the isomorphism are natural in $X$ and $Y$. This is important.*

# The "arrows"-functor $\text{Arr} : C^\textsf{op} \times C \Rightarrow \textsf{Prog}$

We saw in the lecture for Yoneda that for every object $\Gamma$ in $C$ I can give you a functor $\textsf{from}_\Gamma : C \Rightarrow \textsf{Prog}$.

Then, we said that this assignment actually creates a functor into the functor category, which on objects sends $\Gamma \mapsto \textsf{from}_\Gamma$. In order to define it on arrows we said that you need to put an "$\textsf{op}$".

$$よ : C^{\textsf{op}} \to [C,\textsf{Prog}]$$

Today we explain the situation better by introducing this functor here:

$$\text{Arr} : C^\textsf{op} \times C \Rightarrow \textsf{Prog}$$

This functor is sometimes called *the "hom" functor of $C$*, or *the "arrows" functor of $C$.*

- *(Objects.)* I need to send an object of $C^\textsf{op} \times C$, i.e., a pair of two objects $A,B$ of $C$ (remember that in $C^\textsf{op}$ the objects are the same as those of $C$!) to a type. We define $\text{Arr}(A,B) :=$ `Arr_A_B` to be precisely the type of arrows from $A$ to $B$.
- *(Arrows.)* For every two objects of $C^\textsf{op} \times C$,

    say $X := (A,B)$ and $Y := (A',B')$,

    I need to send an arrow of $C^\textsf{op} \times C$, i.e., a pair $(l,r)$ of two arrows,

    one $l : A \to A'$ in $C^\textsf{op}$ and

    one $r : B \to B'$ in $C$.

    to an arrow in $\text{Prog}$, i.e., I need to construct an arrow in $\text{Prog}$, i.e., a program, that goes like this: $$\texttt{Arr\_A\_B} \longrightarrow \texttt{Arr\_A}'\_\texttt{B}'$$

    Remember, that $l : A \to A'$ in $C^\textsf{op}$, which therefore actually comes from an arrow $l : A' \to A$ in $C$.

    So, to recap: given

    1. an arrow $l : A' \to A$, which I think of as a value-in-a-type `l: Arr_A'_A`
    1. an arrow $r : B \to B'$, which I think of as a value-in-a-type `r: Arr_B_B'`
    3. I need to give a program `fn arr(f: Arr_A_B) -> Arr_A'_B'`.

    Conceptually, I have this:

    $$
    A' \xrightarrow{l} A \qquad B \xrightarrow{r} B'
    $$

    and I need to create a program that given an arrow

    $$
    A \xrightarrow{f} B
    $$

    just constructs an arrow

    $$
    A' \xrightarrow{???} B'
    $$

    Here is the arrow:

    $$
    A' \xrightarrow{l} A \xrightarrow{f} B \xrightarrow{r} B'
    $$

    Here is the program:
    ```rust
    fn arr(f: Arr_A_B) -> Arr_A'_B' {
        compose(l, compose(f, r))
    }
    ```
- *(Sends identities to identities.*) ...
- *(Sends compositions to compositions.)* ...

#### A special case

We can do this for every category $C$. In particular, we can do it for $C := \text{Prog}$.

- $\textsf{Arr}_C : C^\textsf{op} \times C \Rightarrow \textsf{Prog}$
- $\textsf{Arr}_\textsf{Prog} : \textsf{Prog}^\textsf{op} \times \textsf{Prog} \Rightarrow \textsf{Prog}$

# $\text{Cat}$ is cartesian closed

*(Claim.)* $\text{Cat}$ is cartesian closed.

- *(Terminal object)*. The terminal category $1$. (we saw this in Lecture 17)
- *(Products)*. The product of $C$ and $D$ is the product category $C \times D$. (we saw this in Lecture 17)
- *(Exponential objects)*. The exponential object from $C$ to $D$ is the functor category $[C,D]$.

### $\text{Cat}$ has exponential objects

1. *(Existence of evaluation)*. There is a functor $\textsf{eval} : A \times [A,B] \to B$, defined like this: on objects $\textsf{eval}(X,F) := F(X)$ we send the pair of an object $X$ of $A$ and a functor $F : A \Rightarrow B$ to the object of $B$ that $F$ sends $X$ to. Exercise: check the rest of the definition.
2. *(Existence of lambda abstraction)*. given a category $H$ and a functor $F : H \times A \Rightarrow B$ we need to build a functor $\Lambda(F) : H \Rightarrow [A,B]$ into the functor category.
    - *(Program on objects.)* $\Lambda(F)(X)$ for some object $X$ of $H$ must be an object of $[A,B]$, so a functor! Let's define this functor.
        - *(Program on objects.)* $\Lambda(F)(X)(Z)$ for some object $Z$ of $A$ is defined as $\Lambda(F)(X)(Z) := F(X,Z)$.
        - *(Program on arrows.)* $\Lambda(F)(X)(f)$ for some arrow $f : Z \to Z'$ of $A$ is defined as $\Lambda(F)(X)(f) := F(\textsf{id}_X,f)$.
    - *(Program on arrows.)* given an arrow $f : X \to Y$, of $H$ I need to construct an arrow of $[A,B]$, i.e., a natural transformation, like this $$\alpha : \Lambda(F)(X) \longrightarrow \Lambda(F)(Y)$$ i.e., a family
    $$\alpha_Z : \Lambda(F)(X)(Z) \longrightarrow \Lambda(F)(Y)(Z)$$
      for every $Z$ in $A$. But considering the way we defined $\Lambda(F)(X)(Z)$, this is just the same as having to build a family of arrows
    $$\alpha_Z : F(X,Z) \longrightarrow F(Y,Z)$$
    so we define $\alpha_Z := F(f,\textsf{id}_Z)$, i.e., we use the program on arrows of $F$, which sends arrows of $H \times X$ (i.e., pairs like $(f, \textsf{id}_Z)$) to arrows in $D$.
4. *(Lambda equation)* ...
5. *(Lambda expansion)* ...

---

*(Consequence.)* You can use the general reasoning of cartesian closed categories to build a lot of functors out of thin air.

For example, we now know that functor

$$よ : C^\textsf{op} \to [C, \text{Prog}]$$

is precisely the curried version of

$$\text{Arr} : C^\textsf{op} \times C \to \text{Prog}$$

---

## op is functorial

### There is a functor $\textsf{op} : \textsf{Cat} \to \textsf{Cat}$.

- *(Program on objects.)* Given a category $C$, I need to give you another category. I give you the category $C^\textsf{op}$.
- *(Program on arrows.)* Given a functor $F : C \Rightarrow D$, I need to give you a functor $F^\textsf{op} : C^\textsf{op} \Rightarrow D^\textsf{op}$.
    - *(Program on objects.)* on objects, $F^\textsf{op}(X) := F(X)$. This works because $D^\textsf{op}$ has the same objects of $D$ and because $X$ works both as an object of $D$ or of $D^\textsf{op}$.
    - *(Program on arrows.)* Given an arrow
        - $f : X \to Y$ of $C^\textsf{op}$, I need to give an arrow
        - $F(X) \to F(Y)$ in $D^\textsf{op}$.

        But this situation is precisely the same as this: I am given an arrow
        - $f : Y \to X$ of $C$, and I need to give an arrow
        - $F(Y) \to F(X)$ in $D$.

        So I can simply define the program on arrows as $F^\textsf{op}(f) := F(f)$.

#### There is a functor $\textsf{op} : [C,D] \to [C^\textsf{op},D^\textsf{op}]^\textsf{op}$.

- *(Program on objects.)* Given a functor $F$, I need to give you an object of $[C^\textsf{op},D^\textsf{op}]^\textsf{op}$, which is the same as an object of $[C^\textsf{op},D^\textsf{op}]$ because $\textsf{op}$ does not change the objects, which is the same as a functor $C^\textsf{op} \Rightarrow D^\textsf{op}$. I decide to give you precisely $F^\textsf{op}$ using exactly the same definition I gave you above.
- *(Program on arrows.)* Given a natural transformation $\alpha : F \Rightarrow G$, I need to give you an arrow of $[C^\textsf{op},D^\textsf{op}]^\textsf{op}$.

    An arrow of $[C^\textsf{op},D^\textsf{op}]$ without $\textsf{op}$ would simply be a natural transformation $\beta : F^\textsf{op} \longrightarrow G^\textsf{op}$... but since there is an $\textsf{op}$ (which I placed there because I know that it's not going to be possible to define the functor if you don't put it!) I need to give you a natural transformation $\beta : G^\textsf{op} \longrightarrow F^\textsf{op}$.

    What is this? Following the definition of natural transformation, it's a family of arrows $$\beta_X : G^\textsf{op}(X) \longrightarrow F^\textsf{op}(X)$$ **in the category $D^\textsf{op}$** (IMPORTANT) for every object $X$ in $C^\textsf{op}$.

    So this means that you need to give a family of arrows
    $$\beta_X : F^\textsf{op}(X) \longrightarrow G^\textsf{op}(X)$$
    **in the category $D$**, without $\textsf{op}$. But remember that the program on objects $F^\textsf{op}(X)$ is just the same as $F(X)$, so we need to give a a family of arrows
    $$\beta_X : F(X) \longrightarrow G(X)$$
    but this is exactly what we assumed to have at the beginning of the paragraph! So, the $\beta$ that we give is just going to be exactly the family $\alpha$ that we obtained as hypothesis.

---
---

Let's build functors out of nothing.

Ingredients:
- Categories $C,D$
- A functor $F : C \Rightarrow D$.
- $\textsf{Arr}_D : D^\textsf{op} \times D \Rightarrow \textsf{Prog}$

We want to build a functor $C^\textsf{op} \times D \Rightarrow \textsf{Prog}$ defined by $(X,Y) \mapsto \texttt{Arr}_D(F(X),Y)$.

1. Knowing that $\textsf{op}$ is functorial, I have a functor $F^\textsf{op} : C^\textsf{op} \Rightarrow D^\textsf{op}$.
2. Knowing that $\textsf{Cat}$ has products, I get for free this usual definition of "parallel arrows":
    - given $F : A \Rightarrow B$,
    - given $G : C \Rightarrow D$,
    - I get $F \times G : A \times C \Rightarrow B \times D$

    so I can put in parallel $F^\text{op}$ with the identity to get a functor

    $F^\textsf{op} : C^\textsf{op} \Rightarrow D^\textsf{op}$

    $F^\textsf{op} \times \textsf{id}_D : C^\textsf{op} \times D \to D^\textsf{op} \times D$.
3. Then I can postcompose with $\textsf{Arr}_D$ to get a functor $C^\textsf{op} \times D \Rightarrow \textsf{Prog}$.

    So, the final functor is $$(F^\textsf{op} \times \textsf{id}_D)\,;\textsf{Arr}_D : C^\textsf{op} \times D \Rightarrow \textsf{Prog}$$

    *Intuition:* $(X,Y) \mapsto \texttt{Arr}_D(F(X),\textsf{id}_D(Y))$.

---

We will use the notation $\texttt{Arr}_D(F^\textsf{op}(-),=)$, using $-$ and $=$ to indicate "placeholders" to indicate what the arguments of these functor is.

# Another example

This is the functor:
$$(F^\textsf{op} \times \textsf{id}_D)\,;\textsf{Arr}_D$$


$$(X,Y) \mapsto \texttt{Arr}_D(X,F(Y))$$
$$\texttt{Arr}_D(-,F(=))$$


$F : C \Rightarrow D$

$\textsf{id}_{D^\textsf{op}} \times F : D^\textsf{op} \times C \Rightarrow D^\textsf{op} \times D$

$$(\textsf{id}_{D^\textsf{op}} \times F)\,;\textsf{Arr}_{D} : D^\textsf{op} \times C \Rightarrow \text{Prog}$$

# Back to adjunctions

Ingredients:
- Categories $C,D$
- A functor $L : C \to D$.
- A functor $R : D \to C$.
- $\textsf{Arr}_C : C^\textsf{op} \times C \Rightarrow \textsf{Prog}$
- $\textsf{Arr}_D : D^\textsf{op} \times D \Rightarrow \textsf{Prog}$

### Definition of adjunction:

there is an isomorphism $$\texttt{Arr}_D(L^\textsf{op}(-),=) \cong \texttt{Arr}_C(-,R(=))$$ in the functor category $[C^\textsf{op}\times D, \text{Prog}]$, where arrows are natural transformations. This takes care of the naturality requirement as well as the requirement that this isomorphism is there for every single $X$ and $Y$.

Explicitly, there are two natural transformations $\theta,\theta^{-1}$. between functors $[C^\textsf{op} \times D,\text{Prog}]$ which compose to the identity.

- $\theta : \texttt{Arr}_D(L^\textsf{op}(-),=) \longrightarrow \texttt{Arr}_C(-,R(=))$
- $\theta^{-1} : \texttt{Arr}_C(-,R(=)) \longrightarrow \texttt{Arr}_D(L^\textsf{op}(-),=)$
- such that $\theta\,;\theta^{-1} = \textsf{id}$,  $\theta^{-1}\,;\theta = \textsf{id}$.

Since these are natural transformations, they are families of arrows

$$
\theta_{A,B} : \texttt{Arr}_D(L(A),B) \to \texttt{Arr}_C(A,R(B)) \\
\theta^{-1}_{A,B} : \texttt{Arr}_C(A,R(B)) \to \texttt{Arr}_D(L(A),B) \\
$$

for every object (i.e., pair) $(A,B)$ of $C^{\textsf{op}} \times D$, so $A$ of $C$ and $B$ of $D$.

# Example 1: exponential adjunction/tensor-hom adjunction

In Lecture 9 we saw this:

$$\texttt{Arr\_HxA\_B} \cong \texttt{Arr\_H\_A⇒B} $$

1. $C := D$
2. $L : C \to C$
3. $R : C \to C$

For every object $A$,

4. $L := A \times -$
5. $R := A \Rightarrow -$

$${A {\times} - \dashv A} \Rightarrow -$$

This is also a characterization for exponential objects:

I always have that $L := A \times -$ is a functor.

*Claim.* A category $C$ is cartesian closed (i.e., has all exponential objects) if and only if there is a right adjoint to $A \times -$ for every single $A$.

# Example 2: terminal/initial objects

1. $C$ category
2. $1$ category
3. There is a functor $! : C \Rightarrow 1$

*(Claim.)* $C$ has a terminal object precisely when $!$ has a right adjoint.

*(Claim.)* $C$ has a initial object precisely when $!$ has a left adjoint.

### Claim 1

1. $C := C, D := 1$
2. $L = {!} : C \Rightarrow 1$,
3. We ask for the existence of $R : 1 \Rightarrow C$
4. We ask for $! \vdash R$.

For every $X$ object of $C$ and every $Y$ object of $1$,

$$\begin{array}{ccc}
() & \longrightarrow & \phantom{R(}()\phantom{)} & \text{in }1\\ \hline \hline
X & \longrightarrow & R(()) & \text{in }C\\
\end{array}
$$

### Claim 2

1. $C := 1, D := D$
2. $R = {!} : D \Rightarrow 1$,
3. We ask for the existence of $L : 1 \Rightarrow C$
4. We ask for $L \vdash {!}$.

For every $X$ object of $1$ and every $Y$ object of $D$,

$$\begin{array}{ccc}
L(()) & \longrightarrow & Y & \text{in }D\\ \hline \hline
\phantom{L(}()\phantom{)} & \longrightarrow & () & \text{in }1\\
\end{array}
$$


# Example 3: products/coproducts

1. $C$ category
2. We know that there is a functor $\delta := \langle \textsf{id}_C, \textsf{id}_C \rangle : C \Rightarrow C \times C$,

### Claim

0. $C := C$, $D := C \times C$,
1. $L := \delta$,
2. We ask for the existence of $R : C \times C \Rightarrow C$

For every $X$ in $C$ and for every $Y$ in $C \times C$,

$$\begin{array}{ccc}
(X,X) & \longrightarrow & \phantom{R(}Y\phantom{)} & \text{in }C \times C\\ \hline \hline
\phantom{L(}X\phantom{)} & \longrightarrow & R(Y) & \text{in }C\\
\end{array}
$$

For every $X$ in $C$ and for every $(A,B)$ in $C \times C$,

$$\begin{array}{ccc}
(X,X) & \longrightarrow & \phantom{R(}(A,B)\phantom{)} & \text{in }C \times C\\ \hline \hline
\phantom{L(}X\phantom{)} & \longrightarrow & R(A,B) & \text{in }C\\
\end{array}
$$

An arrow as above is just a pair of arrow (following the definition of product of categories)

$$\begin{array}{c}
 X \longrightarrow A \text{ in } C \qquad  X \longrightarrow B \text{ in } C \\ \hline \hline
\phantom{L(}X\phantom{)} \longrightarrow R(A,B) \text{ in }C\\
\end{array}
$$


### Claim

0. $C := C \times C$, $D := C$,
1. $R := \delta$,
2. We ask for the existence of $L : C \times C \Rightarrow C$

For every $X$ in $C \times C$ and for every $Y$ in $C$,

$$\begin{array}{ccc}
L(X) & \longrightarrow & Y & \text{in }C\\ \hline \hline
X & \longrightarrow & (Y,Y) & \text{in }C \times C
\end{array}
$$

For every $(A,B)$ in $C \times C$ and for every $Y$ in $C$,

$$\begin{array}{ccc}
L(A,B) & \longrightarrow & Y & \text{in }C\\ \hline \hline
(A,B) & \longrightarrow & (Y,Y) & \text{in }C \times C
\end{array}
$$

An arrow as above is just a pair of arrow (following the definition of product of categories)

$$\begin{array}{c}
L(A,B) \longrightarrow Y \text{ in }C\\ \hline \hline
 A \longrightarrow Y \text{ in } C \qquad  B \longrightarrow Y \text{ in } C \\
\end{array}
$$
