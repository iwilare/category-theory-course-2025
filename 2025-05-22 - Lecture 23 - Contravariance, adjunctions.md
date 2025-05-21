
# Adjunctions

*(Definition.)* Take two categories $C$ and $D$ with two functors $L : C \to D$ and $R : D \to C$.
We say that $F$ is left adjoint to $G$, or equivalently, that $G$ is right adjoint to $F$, if for every $X$ in $C$ and $Y$ in $D$ there is an isomorphism in $\textsf{Prog}$ between these two types of arrows: for every $X$ in $D$ and every $Y$ in $C$ there is a bijection between

$$\begin{array}{ccc}
L(X) & \longrightarrow & \phantom{R(}Y\phantom{)} & \text{in }D\\ \hline \hline
\phantom{L(}X\phantom{)} & \longrightarrow & R(Y) & \text{in }C\\
\end{array}
$$

The two types being isomorphic are `ArrC_(L_obj(X))_Y` and `ArrD_X_(R_obj(Y))`, for every object `X` and `Y`.

*Moreover, the two programs witness the isomorphism are natural in $X$ and $Y$. This is important.*

# The "arrows"-functor $\text{Arr} : C^\textsf{op} \times C \to \textsf{Prog}$

We saw in the lecture for Yoneda that for every object $\Gamma$ in $C$ I can give you a functor $\textsf{from}_\Gamma : C \to \textsf{Prog}$.

Then, we said that this assignment actually creates a functor into the functor category, which on objects sends $\Gamma \mapsto \textsf{from}_\Gamma$. In order to define it on arrows we said that you need to put an "$\textsf{op}$".

$$よ : C^{\textsf{op}} \to [C,\textsf{Prog}]$$

Today we explain the situation better by introducing this functor here:

$$\text{Arr} : C^\textsf{op} \times C \to \textsf{Prog}$$

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

    1. an arrow $l : A' \to A$, which I think of as a value-in-a-type `f: Arr_A'_A`
    1. an arrow $r : B \to B'$, which I think of as a value-in-a-type `f: Arr_B_B'`
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

# $\text{Cat}$ is cartesian closed

The exponential object from $C$ to $D$ of two objects $C,D$ of $\text{Cat}$ (i.e., categories) is precisely the functor category $[C,D]$.

*(Claim.*) 

The functor 

$$よ : C^\textsf{op} \to [C, \text{Prog}]$$

is precisely the curried version of 

$$\text{Arr} : C^\textsf{op} \times C \to \text{Prog}$$