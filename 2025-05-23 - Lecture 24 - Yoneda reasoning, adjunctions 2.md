# 2025-05-23 - Lecture 24 (Yoneda reasoning, more on adjunctions)

# Yoneda reasoning

Recall the Yoneda embedding theorem:

> For every $\Gamma,\Delta$ objects of $C$, there is an isomorphism of types like this:
> $$\texttt{Arr[C,Prog]\_fromΓ\_fromΔ} \cong \texttt{Arr\_Δ\_Γ}$$
> $$\texttt{Arr[Cᵒᵖ,Prog]\_intoΓ\_intoΔ} \cong \texttt{Arr\_Γ\_Δ}$$

*Observation:* another equivalent way to write the $\texttt{intoΓ}$ and $\texttt{fromΓ}$ functors is like this:

$$
\textsf{from}_Γ = \textsf{Arr}_C(\Gamma, -) \\
\textsf{into}_Γ = \textsf{Arr}_C(-, \Gamma)
$$

Why? Because they literally defined as giving you, for an object $\Delta$, the types `Arr_Δ_Γ`, so we can put the placeholder $-$ in the other argument.

So, $\textsf{Arr}$ "generlizes" both $\textsf{into}$ and $\textsf{from}$ at the same time.

# Yoneda isomorphism

A straightforward consequence of the theorem above is this one:

If

$$
\textsf{Arr}_C(\Gamma,-) \cong \textsf{Arr}_C(\Delta,-)
$$

(i.e., they are isomorphic as objects of $[C,\text{Prog}]$), then $\Gamma \cong \Delta$ as objects of $C$.

Similarly, if

$$
\textsf{Arr}_C(-,\Gamma) \cong \textsf{Arr}_C(-,\Delta)
$$

(i.e., they are isomorphic as objects of $[C^\textsf{op},\text{Prog}]$), then $\Gamma \cong \Delta$ as objects of $C$.

*Slogan: two objects are isomorphic precisely when their $\textsf{into}$ (or $\textsf{from}$) functors are (naturally) isomorphic.*

We call these *Yoneda isomorphisms*.

---

*Proof.* (We treat the $\textsf{into}$ case because there's no swapping.) (This proof requires a technical adjustement in the proof of the Yoneda lemma)

Assume that $\textsf{Arr}_C(-,\Gamma) \cong \textsf{Arr}_C(-,\Delta)$, was showing using two arrows (i.e., natural transformations because they are arrows of $[C^\textsf{op},\text{Prog}]$). Explicitly, this means that:

- $\alpha : \textsf{into}_\Gamma \longrightarrow \textsf{into}_\Delta$
- $\alpha^{-1} : \textsf{into}_\Delta \longrightarrow \textsf{into}_\Gamma$
- such that $\alpha \,; \alpha^{-1} = \textsf{id}$,
- and $\alpha^{-1} \,; \alpha = \textsf{id}$.

I want show that $\Gamma \cong \Delta$.

Call the two isomorphisms of the Yoneda embedding theorem like this (note! These are arrows of $\text{Prog}$ because we're talking about the types of arrows), instantiated for the specific objects $\Delta,\Gamma$ that we're working with now.

Take the program on arrows of the Yoneda embedding:
- $よ_{\Gamma,\Delta} : \textsf{Arr}(\Gamma,\Delta) \to \textsf{Arr}_{[C^\textsf{op},\text{Prog}]}(\textsf{into}_\Gamma,\textsf{into}_\Delta)$

It turns out (check this!!! important technical detail) that the Yoneda embedding theorem says that for every $\Gamma,\Delta$ this specific program on arrows has an inverse arrow:

- $よ^{-1}_{\Gamma,\Delta} : \textsf{Arr}_{[C^\textsf{op},\text{Prog}]}(\textsf{into}_\Gamma,\textsf{into}_\Delta) \to \textsf{Arr}(\Gamma,\Delta)$
- such that $よ_{\Gamma,\Delta}\,;よ_{\Gamma,\Delta}^{-1} = \textsf{id}$,
- and $よ_{\Gamma,\Delta}^{-1} \,; よ_{\Gamma,\Delta} = \textsf{id}$.

Then, I obtain these:

- $よ^{-1}_{\Gamma,\Delta}(\alpha) : \Gamma \to \Delta$
- $よ^{-1}_{\Delta,\Gamma}(\alpha^{-1}) : \Delta \to \Gamma$

Let's show that these two compose to the identity.

$$
\begin{array}{lll}
& よ^{-1}_{\Gamma,\Delta}(\alpha) \,; よ^{-1}_{\Delta,\Gamma}(\alpha^{-1}) \\
= & \textsf{id}(よ^{-1}(\alpha) \,; よ^{-1}(\alpha^{-1})) & \text{($よ\,;よ^{-1} = \textsf{id}$)} \\
= & よ^{-1}(よ(よ^{-1}(\alpha) \,; よ^{-1}(\alpha^{-1}))) & \text{(よ is a functor)}\\
= & よ^{-1}(よ(よ^{-1}(\alpha)) \,; よ(よ^{-1}(\alpha^{-1}))) & \text{($よ^{-1}\,;よ = \textsf{id}$, twice)}\\
= & よ^{-1}(\alpha \,; \alpha^{-1}) & \text{($\alpha\,;\alpha^{-1} = \textsf{id}$)}\\
= & よ^{-1}(\textsf{id}) & \text{(よ is a functor, so respects identities)} \\
= & よ^{-1}(よ(\textsf{id})) & \text{($よ\,;よ^{-1} = \textsf{id}$)}\\
= & \textsf{id} \\
\end{array}
$$

The other direction is analogous.

# Recall: adjunctions for (co)products

*Theorem.* There is an isomorphism of functors $[C,\text{Prog}]$ /$[C^{\textsf{op}},\text{Prog}]$ as follows, for every $A$ and $B$.

*Proof.* Follow directly from the presentation of products as adjunctions.

1. $$\textsf{Arr}_{C}(-, 1) \cong 1$$
2. $$\textsf{Arr}_{C}(0, -) \cong 1$$
3. $$\textsf{Arr}_{C}(-, A \times B) \cong \textsf{Arr}_{C}(-, A) \times \textsf{Arr}_{C}(-, B) $$
4. $$\textsf{Arr}_{C}(A + B, -) \cong \textsf{Arr}_{C}(A, -) \times \textsf{Arr}_{C}(B, -) $$
5. $$\textsf{Arr}_{C}(A \times -, =) \cong \textsf{Arr}_{C}(-, A \Rightarrow =)$$

To be fully clear, $1$ on the right here refers to the functor $\textsf{const}_{1} : C^{\textsf{op}} \Rightarrow \text{Prog}$ defined by sending $X \mapsto \texttt{Unit}$.

# The point of the next exercises:

Properties of structures defined by adjunctions (or in general, using $\textsf{into}$/$\textsf{from}$ functors) *in any category $C$*, follow almost directly from the fact that $\textsf{Prog}$, *specifically*, has these properties.

In particular, we use *chains of isomorhisms* between $\textsf{into}$/$\textsf{from}$ functors, and then, by the Yoneda isomorphism, we conclude that two objects must be isomorphic. We call all of these steps together *Yoneda reasoning*.

# Symmetry of products

For every category $C$ which has all products, then for every $A,B$,

$$
\begin{array}{lll}
  & \textsf{Arr}(-, A \times B) & \text{(adjunction for products)}\\
\cong & \textsf{Arr}(-, A) \times \textsf{Arr}(-, B) & \textbf{(products in *$\text{Prog}$* are symmetric)}\\
\cong & \textsf{Arr}(-, B) \times \textsf{Arr}(-, A) & \text{(adjunction for products)}\\
\cong & \textsf{Arr}(-, B \times A) & \\
\end{array}
$$

Hence, by Yoneda isomorphism, $A \times B \cong B \times A$.

# Associativity of products

For every category $C$ which has all products, then for every $A,B,C$,

$$
\begin{array}{lll}
  & \textsf{Arr}(-, (A \times B) \times C) & \text{(adjunction for products)}\\
\cong & \textsf{Arr}(-, A \times B) \times \textsf{Arr}(-, C) & \text{(adjunction for products)}\\
\cong & \left(\textsf{Arr}(-, A) \times \textsf{Arr}(-, B) \right) \times \textsf{Arr}(-, C) & \textbf{(products in *$\text{Prog}$* are associative)}\\
\cong & \textsf{Arr}(-, A) \times (\textsf{Arr}(-, B) \times \textsf{Arr}(-, C)) & \text{(adjunction for products)}\\
\cong & \textsf{Arr}(-, A) \times \textsf{Arr}(-, B \times C) & \text{(adjunction for products)}\\
\cong & \textsf{Arr}(-, A \times (B \times C)) & \\
\end{array}
$$

Hence, by Yoneda isomorphism, $A \times B \cong B \times A$.

# Symmetry of coproducts

For every category $C$ which has all coproducts, then for every $A,B$,

$$
\begin{array}{lll}
  & \textsf{Arr}(A + B, -) & \text{(adjunction for coproducts)}\\
\cong & \textsf{Arr}(A, -) \times \textsf{Arr}(B, -) & \textbf{(products in *$\text{Prog}$* are symmetric)}\\
\cong & \textsf{Arr}(B, -) \times \textsf{Arr}(A, -) & \text{(adjunction for coproducts)}\\
\cong & \textsf{Arr}(B + A, -) & \\
\end{array}
$$

Hence, by Yoneda isomorphism, $A + B \cong B + A$.

# Distributivity in categories

Assume that we are in a cartesian closed category, then it is automatically distributive (we knew this already), i.e., a certain canonical arrow is an isomorphism, as follows for every $A,B,C$:

$$(A \times B) + (A \times C) \cong A \times (B + C)$$

*Proof, by Yoneda reasoning.*

$$
\begin{array}{lll}
  & \textsf{Arr}((A \times B) + (A \times C), -) & \text{(adjunction for coproducts)}\\
  & \textsf{Arr}(A \times B, -) \times \textsf{Arr}(A \times C, -) & \text{(exponential adjunction on $A$)}\\
  & \textsf{Arr}(B, A \Rightarrow -) \times \textsf{Arr}(C, A \Rightarrow -) & \text{(adjunction for coproducts)}\\
  & \textsf{Arr}(B + C, A \Rightarrow -) & \text{(exponential adjunction on $A$)}\\
  & \textsf{Arr}(A \times (B + C), -) & \text{(exponential adjunction on $A$)}\\
\end{array}
$$

# Adjoints are unique up to natural isomorphism

We prove that left adjoints are unique up to isomorphism (in the functor category $[C,D]$). To do this we use Yoneda reasoning.

Imagine that I am given a situation where $L \dashv R$ and $L' \dashv R$.

Then, we show that for every $L'(X) \cong L(X)$. Moreover, one can easily check that all these isomorphisms are natural in $X$, hence $L(-) \cong L'(-)$.

$$
\begin{array}{lll}
  & \textsf{Arr}(L'(-), =) & \text{($L'$ is a left adjoint)}\\
 \cong & \textsf{Arr}(-, R(=)) & \text{($L$ is a left adjoint)}\\
 \cong & \textsf{Arr}(L(-), =) & \\
\end{array}
$$

Therefore $L'(-) \cong L(-)$ by Yoneda reasoning. (technically we can only do this for a concrete $Y := L(X)$ for now but of course it can be generalized to apply to this case too.)

We can also do it for a concrete $X$:
$$
\begin{array}{lll}
  & \textsf{Arr}(L'(X), -) & \text{($L'$ is a left adjoint)}\\
 \cong & \textsf{Arr}(X, R(-)) & \text{($L$ is a left adjoint)}\\
 \cong & \textsf{Arr}(L(X), -) & \\
\end{array}
$$


Right adjoints are also unique by the same reasoning:
$$
\begin{array}{lll}
  & \textsf{Arr}(-, R(=)) & \text{($R$ is a right adjoint)}\\
  \cong & \textsf{Arr}(L(-), =) & \text{($R'$ is a right adjoint)}\\
  \cong & \textsf{Arr}(-, R'(=)) & \\
\end{array}
$$

# Right adjoints preserve terminal objects and products

A functor $F : C \Rightarrow D$ is said to preserve products when

$$F(A \times B) \cong F(A) \times F(B)$$

A functor $F : C \Rightarrow D$ is said to preserve terminal objects when

$$F(1) \cong 1$$

Imagine that we are given an adjunction $L \dashv R$. Then $R$ preserves products and terminal objects (in general, right adjoint-like structures).

$$
\begin{array}{lll}
  & \textsf{Arr}(-, R(A \times B)) & \text{($R$ is a right adjoint)}\\
  \cong & \textsf{Arr}(L(-), A \times B) & \text{(adjunction for products)}\\
  \cong & \textsf{Arr}(L(-), A) \times \textsf{Arr}(L(-), B) & \text{($R$ is a right adjoint)}\\
  \cong & \textsf{Arr}(-, R(A)) \times \textsf{Arr}(-, R(B)) & \text{(adjunction for products)}\\
  \cong & \textsf{Arr}(-, R(A) \times R(B)) &\\
\end{array}
$$

$$
\begin{array}{lll}
  & \textsf{Arr}(-, R(1)) & \text{($R$ is a right adjoint)}\\
 \cong & \textsf{Arr}(L(-), 1) & \text{(adjunction for terminal objects)}\\
 \cong & 1 & \text{(adjunction for terminal objects)}\\
 \cong & \textsf{Arr}(-, 1) & \\
\end{array}
$$

### Left adjoints preserve coproducts and initial objects.

### People say: RAPL, L = limits

# Unit-counit presentation of adjunctions

Imagine that someone gives me an adjunction $L \dashv R$, for $L : C \Rightarrow D$, $R : D \Rightarrow C$.

Recall that this means that for every $X$ in $C$ and $Y$ in $D$ there is an isomorphism of types as arrows, and it's natural a precise sense.

$$
\theta_{A,B} : \texttt{Arr}_D(L(A),B) \to \texttt{Arr}_C(A,R(B)) \\
\theta^{-1}_{A,B} : \texttt{Arr}_C(A,R(B)) \to \texttt{Arr}_D(L(A),B) \\
$$

There are two special natural transformations that I associate to every adjunction, called $\textsf{unit}$ and $\textsf{counit}$.


$$
\begin{array}{ll}
\textsf{unit} : \textsf{id}_C \longrightarrow L\,;R \\
\textsf{unit}_X : X \to R(L(X)) \\
\end{array}
$$

$$
\begin{array}{ll}
\textsf{counit} : R\,;L \longrightarrow \textsf{id}_D \\
\textsf{counit}_X : L(R(X)) \to X \\
\end{array}
$$

Take the two $\theta,\theta^{-1}$ above and let's instantiate them in a specific way.

in particular for $A := X$ and $B := L(X)$ for every object $X$ of $C$

$$
\theta_{X,L(X)} : \texttt{Arr}_D(L(X),L(X)) \to \texttt{Arr}_C(X,R(L(X)))
$$

in particular for $A := R(X)$ and $L := X$ for every object $X$ of $D$

$$
\theta^{-1}_{R(X),X} : \texttt{Arr}_C(R(X),R(X)) \to \texttt{Arr}_D(L(R(X)),X)
$$

So we define:

$$
\begin{array}{ll}
\textsf{counit}_X := \theta_{X,L(X)}(\textsf{id}_X) \\
\textsf{unit}_X := \theta^{-1}_{R(X),X}(\textsf{id}_X)
\end{array}
$$

*Exercise.* show that these are *natural* transformations.

It turns out that these satisfy some reasonable equations, called *triangle identities* or *zig-zag identities*:


> $$
> \begin{array}{ll}
> \textsf{unit}_X : X \to R(L(X)) \\
> \textsf{counit}_X : L(R(X)) \to X \\
> \end{array}
> $$

1.
  $$
    \begin{array}{c}
      L(X) & \xrightarrow{L(\textsf{unit}_X)} & L(R(L(X))) &  \xrightarrow{\textsf{counit}_{L(X)}} & L(X) \\
    \end{array}
    \\
    =
    \\
    \begin{array}{c}
      L(X) & \xrightarrow{\phantom{......................}\textsf{id}_{L(X)}\phantom{......................}} L(X) \\
    \end{array}
  $$
2.
  $$
    \begin{array}{c}
      R(X) & \xrightarrow{\textsf{unit}_{R(X)}} & R(L(R(X))) & \xrightarrow{R(\textsf{counit}_X)}  & R(X) \\
    \end{array}
    \\
    =
    \\
    \begin{array}{c}
      R(X) & \xrightarrow{\phantom{......................}\textsf{id}_{R(X)}\phantom{......................}} R(X) \\
    \end{array}
  $$

We can express these purely using horizontal composition of natural transformations:

1. $(\textsf{unit} * \textsf{id}_L) \,; (\textsf{id}_L * \textsf{counit}) = \textsf{id}_L$
1. $(\textsf{id}_R * \textsf{unit}) \,; (\textsf{counit} * \textsf{id}_R) = \textsf{id}_R$

## Unit and counit + some equations characterize adjunctions.

There is an equivalent presentation of adjunctions.

1. A natural transformation $\textsf{unit} : \textsf{id}_C \rightarrow L \,; R$,
2. A natural transformation $\textsf{counit} : R \,; L \rightarrow \textsf{id}_D$,
3. The zig-zag identity 1 is satisfied.
4. The zig-zag identity 2 is satisfied.

*Sketch of proof.*

Fix two functors $L : C \Rightarrow D$, $R : D \Rightarrow C$, and two objects $X$ in $C$ and $Y$ in $D$.

Imagine someone gives me $\textsf{unit}$ and $\textsf{counit}$ (and tells me the zig-zag identities).

> $$
> \begin{array}{ll}
> \textsf{unit}_X : X \to R(L(X)) \\
> \textsf{counit}_X : L(R(X)) \to X \\
> \end{array}
> $$
Imagine someone gives me an arrow $f : X \to R(Y)$. I need to construct an arrow $L(X) \to Y$.

Imagine someone gives me an arrow $g : L(X) \to Y$. I need to construct an arrow $X \to R(Y)$.

# Monads from adjunctions

*Sketch of proof using graphical language and restricting to the case where $C = D$.*
