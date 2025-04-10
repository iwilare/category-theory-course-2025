# 2025-04-10 - Lecture 15 (Natural transformations 2, Yoneda)

# Working with natural transformations

### The identity natural transformation

For any functor $F : C \Rightarrow D$, there is always a natural transformation $\alpha : F \longrightarrow F$ for any functor

$$
    \begin{array}{ccccccccc}
    \phantom{F(f)} F(X) & \xrightarrow{\phantom{iii}\textsf{id}_X\phantom{iii}} & F(X) \phantom{F(f)} & \\
    {F(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {F(f)}\\
    \phantom{F(f)} F(Y) & \xrightarrow{\phantom{iii}\textsf{id}_Y \phantom{iii}} & F(Y) \phantom{F(f)} \\
    \end{array}
$$

So the natural transformation $\textsf{g} : F \longrightarrow F$ is defined as the family of arrows $\textsf{g}_X :  F(X) \longrightarrow F(X)$ is precisely given by the identity on $F(X)$, so $\textsf{g}_X := \textsf{id}_{F(X)}$. We typically choose to call this natural transformation, instead of $\textsf{g}$, we call it $\textsf{id}$, a bit confusingly.

### Composition of natural transformations (also called "vertical composition")

Take three functors $F,G,H : C \Rightarrow D$, all from the same category to the same category.

Imagine we have a natural transformation $\alpha : F \longrightarrow G$ and a natural transformation $\beta : G \longrightarrow H$.

Then, I can cook you up a new natural transformation $\gamma : F \longrightarrow H$, which I write like as $\alpha \,; \beta$ (note: this is NOT a composition of any category yet. What I wrote does not even make sense! It's just notation.)

The family $\gamma$ is defined like this:
$$\gamma_X := \alpha_X \,; \beta_X$$

Is this a *natural* transformation?

$$
\begin{array}{ccccccccccc}
\phantom{F(f)} F(X) & \xrightarrow{\phantom{ii}\alpha_X\phantom{ii}} & G(X) & \xrightarrow{\phantom{ii}\beta_X\phantom{ii}} & H(X) \phantom{F(f)} \\
{F(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {G(f)} & \raisebox{2pt}{\tiny =} & \downarrow {H(f)} \\
\phantom{F(f)} F(Y) & \xrightarrow{\phantom{ii}\alpha_Y\phantom{ii}} & G(Y) & \xrightarrow{\phantom{ii}\beta_Y\phantom{ii}} & H(Y) \phantom{F(f)} \\
\end{array}
$$



$$
\begin{array}{ccccccc}
\phantom{F(f)} F(X) & \xrightarrow{\phantom{ii} \alpha_X \,;\, \beta_X \phantom{ii}} & H(X) \phantom{F(f)} \\
{F(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {H(f)} \\
\phantom{F(f)} F(Y) & \xrightarrow{\phantom{ii} \alpha_Y \,;\, \beta_Y \phantom{ii}} & H(Y) \phantom{F(f)} \\
\end{array}
$$

**Graphical intuition for why it's called vertical intuition.**

### Composition of natural transformations (also called "horizontal composition")

Remember: we know how to compose functors together.

Now, imagine this situation: three categories,
- $C$,
- $D$,
- $E$,

Four functors:
- $F, F' : C \Rightarrow D$
- $G, G' : D \Rightarrow E$

Two natural transformations:
- $\alpha : F \longrightarrow F'$
- $\beta : G \longrightarrow G'$

Then, I cook up a new natural transformation like this

- $\gamma : (F\,;G) \longrightarrow (F'\,; G')$

which we will denote as $\gamma := \alpha * \beta$.

$$
\begin{array}{rcl}
(\beta \ast \alpha)_X &:=& G(\alpha_X) \,; \beta_{F'(X)}  \\
\end{array}
$$

We need to check that this family of arrows is indeed a natural transformation.
That is, we need to show that for every object $X,Y$ in $C$ and any arrow $f : X \to Y$, the following square commutes:

$$
\begin{array}{ccccccc}
\phantom{F(f)} G(F(X)) & \xrightarrow{\phantom{ii} G(\alpha_X) \phantom{ii}} & G(F'(X)) & \xrightarrow{\phantom{ii} \beta_{F'(X)} \phantom{ii}} & G'(F'(X)) \phantom{F(f)} \\
G(F(f)) \downarrow & \raisebox{2pt}{\tiny =} & \downarrow G(F'(f)) & \raisebox{2pt}{\tiny =} & \downarrow G'(F'(f)) \\
\phantom{F(f)} G(F(Y)) & \xrightarrow{\phantom{ii} G(\alpha_Y) \phantom{ii}} & G(F'(Y)) & \xrightarrow{\phantom{ii} \beta_{F'(Y)} \phantom{ii}} & G'(F'(Y)) \phantom{F(f)} \\
\end{array}
$$

$$
\begin{array}{ccc}
\phantom{F(f)} G(F(X)) & \xrightarrow{\phantom{ii} (\beta \ast \alpha)_X \phantom{ii}} & G'(F'(X)) \phantom{F(f)} \\
G(F(f)) \downarrow & \raisebox{2pt}{\tiny =} & \downarrow G'(F'(f)) \\
\phantom{F(f)} G(F(Y)) & \xrightarrow{\phantom{ii} (\beta \ast \alpha)_Y \phantom{ii}} & G'(F'(Y)) \phantom{F(f)} \\
\end{array}
$$

**Graphical intuition for why it's called horizontal composition.**

# The functor category $[C,D]$

Take any two categories $C,D$ fixed. There is a category written $[C,D]$, called the *"category of functors from $C$ to $D$"* defined like this:

- *(Objects.)* There is value in the type of objects for every single possible functor $F : C \Rightarrow D$,
- *(Arrows.)* Given two functors $F,G$, you have a value in the type of arrows from $F$ to $G$ for every possible natural transformation $\alpha : F \Rightarrow G$. Equality of arrows is defined to be the fact that for every possible object $X$, the two arrows $\alpha_X$ and $\beta_X$ are the same.
- *(Identities.)* We need to find an arrow from $F$ to $F$; there is indeed an identity natural transformation $\textsf{id} : F \Rightarrow F$.
- *(Composition.)* Given two natural transformations $\alpha : F \longrightarrow G$, $\beta : G \longrightarrow H$, we need to find another natural transformation $F \longrightarrow H$: this is precisely vertical composition!
- *(Identity left/right.)*: check this!!
- *(Associativity.)*: check this!!

# Special functors: the arrows-from-$Γ$ functor

Take a category $C$.

Take a specific object $\Gamma$ of $C$, any object whatsoever works.

I cook up a functor $\textsf{into}_\Gamma : C \to \text{Prog}$.

- *(Program on objects.)* Given an object $X$ of $C$, I send it to the object of $\text{Prog}$ type `Arr_Γ_X`. So we define `IntoΓ(X) := Arr_Γ_X`.
- *(Program on arrows.)* This is what the program on arrows does:

```rust
fn IntoΓ_arrows(f: Arr_X_Y) -> ProgArr_IntoΓ(X)_IntoΓ(Y) {
    "fn IntoΓ_arrows_f(p: Arr_Γ_X) -> Arr_Γ_Y {
        compose(p, f)
    }"
}
```

Keep this in mind for the next section: effectively, if we think of programs and function types as being the same thing...

```rust
// *important for later!!*
IntoΓ_arrows(f)(p) = compose(p, f)
```

- *(Respects identities)*
    ```rust
    "fn IntoΓ_arrows_f(p: Arr_Γ_X) -> Arr_Γ_X {
        compose(p, id)
    }"
    // this is equivalent to... the identity arrow on Prog!
    // so when f=identity I send it to the identity on Prog.
    "fn IntoΓ_arrows_f(p: Arr_Γ_X) -> Arr_Γ_X {
        p
    }"
    ```
- *(Respects compositions)* Same story.

# Yoneda's lemma

Take a functor $F : C \Rightarrow \text{Prog}$. Take an object $\Gamma$ of $C$.

So:
- $F$ is a functor $C$ to $\text{Prog}$.
- $\textsf{into}_\Gamma$ is a functor $C$ to $\text{Prog}$.

Realisations:

- There is a type $F(\Gamma)$.
- Take the type `Arr[C,Prog]_IntoΓ_F`, the type of arrows in the category $[C,\text{Prog}]$: this was defined to be the type that has a value for each possible natural transformation $\alpha : \textsf{into}_\Gamma \longrightarrow F$.

*(Yoneda's lemma.)* the two types above are isomorphic in $\text{Prog}$: $\texttt{Arr[C,Prog]\_intoΓ\_F} \cong F(\Gamma)$.

*In other words,* **There is a one-to-one correspondence between values of $F(\Gamma)$ and natural transformations with the signature $\alpha : \textsf{into}_\Gamma \longrightarrow F$.**

*Proof.*

We need to create an isomorphism in Prog, so we need to create two programs in Rust.

- $\texttt{natToFΓ} : \texttt{Arr\_intoΓ\_F} \to F(\Gamma)$

```rust
fn natToFΓ(alpha: Arr[C,D]_IntoΓ_F) -> F(Γ) {
    // Recall:
    //   alpha is a natural transformation from intoΓ to F.
    //   so, it's a polymorphic function like this:
    //
    //   fn alpha<X>(a: intoΓ(X)) -> F(X) { ... }
    //   fn alpha<X>(a: Arr_Γ_X) -> F(X) {
    //      ...
    //   }
    //
    //   which moreover is natural, i.e., makes the square commute

    // In particular,
    //      alpha<Γ> : Arr_Γ_Γ -> F(Γ)
    // is a perfectly valid concrete program.

    // So I can call this program on the special value id of Γ.

    alpha<Γ>(id[Γ])
}

fn FΓtoNat(k: F(Γ)) -> Arr[C,D]_IntoΓ_F {
    // So, here I am given a value k of type F(Γ)
    // and I need to construct a natural transformation, i.e., a parametric function:
    //
    //   fn alpha<X>(f: intoΓ(X)) -> F(X) { ... }
    //   fn alpha<X>(a: Arr_Γ_X) -> F(X) {
    //      ...
    //   }
    //
    // (remember how intoΓ is defined: on X, it gives me the type Arr_Γ_X)
    // and this family must also be natural, i.e., make the square commute.

    // Key point: F is a functor.
    fn alpha<X>(m: Arr_Γ_X) -> F(X) {
        // Recall, definition of functor:
        //
        //  given
        //
        //    m: Arr_Γ_X
        //
        // we get
        //
        //    F_arrows(m) : F(Γ) -> F(X), an arrow in Prog.
        //
        // gives me an arrow of the target category D if F goes from C to D.
        // The target category of our F in this case is Prog, so this gives me an arrow/a program! From the type F(Γ) to the type F(X).

        // So, I can call this program exactly on the value k: F(Γ).

        F_arrows(m)(k)
    }
    alpha
}
```

Without comments:
```rust
fn natToFΓ(alpha: Arr[C,D]_IntoΓ_F) -> F(Γ) {
    alpha<Γ>(id[Γ])
}
fn FΓtoNat(k: F(Γ)) -> Arr[C,D]_IntoΓ_F {
    fn alpha<X>(m: Arr_Γ_X) -> F(X) {
         F_arrows(m)(k)
    }
    alpha
}
```

**Key sentence: in natToFΓ, we take an `alpha` an instantiate it on the identity of `Γ`.**

**Key sentence: in FΓtoNat, `alpha` takes an arrow `m` and "transports" `k` along that arrow, using the program on arrows of $F$.**

We have to check that the specific `alpha` given by `FΓtoNat` really is a natural family of arrows of `Prog`:

- For every arrow $f : X \to Y$ in $C$, this square *AGAIN in our beloved category $\text{Prog}$* commutes:
    $$
    \begin{array}{ccccccccc}
    \phantom{\textsf{into}_\Gamma(f)} \textsf{into}_\Gamma(X) & \xrightarrow{\phantom{iii}\alpha_X\phantom{iii}} & F(X) \phantom{F(f)} & \\
    {\textsf{into}_\Gamma(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {F(f)}\\
    \phantom{\textsf{into}_\Gamma(f)} \textsf{into}_\Gamma(Y) & \xrightarrow{\phantom{iii}\alpha_Y \phantom{iii}} & F(Y) \phantom{F(f)} \\
    \end{array}
    $$

Let's write this down as program equivalence:

(The `k: F(Γ)` is fixed, we're checking inside of the definition of `FΓtoNat` that this alpha is indeed a natural transformation.)

For every value of `m: intoΓ(X) = Arr_Γ_X`, so $m : Γ \to X$ is an arrow in $C$,

```
    F_arrows(f)(alpha<X>(m))
  = alpha<Y>(IntoΓ_arrows(f)(m))
```

Start from the top:

```rust
    F_arrows(f)(alpha<X>(m))    // Definition of alpha<X>
  = F_arrows(f)(F_arrows(m)(k))
```

So, we obtained the same thing! So we conclude that the `alpha` we gave really is a natural transformation.

---

Whenever we have a generic $\alpha : \textsf{into}_\Gamma : F$, the naturality equation looks like this: for every $m : \Gamma \to X$ and $f : X \to Y$

```rust
    F_arrows(f)(alpha<X>(m))
  = alpha<Y>(IntoΓ_arrows(f)(m))
  = alpha<Y>(compose(m, f))
```




---

Now, we need to check that `natToFΓ` and `FΓtoNat` really compose to the identity in both ways.

Again, we have to reason by program equivalence:

1. For any value `k: F(Γ)`, we need to check that:

    ```rust
    natToFΓ(FΓtoNat(k)) = k
    ```

    The natural transformation given by `FΓtoNat(k)` is by definition this one:

    ```rust
    fn FΓtoNat(k: F(Γ)) -> Arr[C,D]_IntoΓ_F {
        fn alpha<X>(m: Arr_Γ_X) -> F(X) {
            F_arrows(m)(k)
        }
        alpha
    }
    ```

    What does `natToFΓ`? It takes the `alpha` and runs it on the identity.

    ```rust
    fn natToFΓ(alpha: Arr[C,D]_IntoΓ_F) -> F(Γ) {
        alpha<Γ>(id[Γ])
    }
    ```

    So,

    ```rust
    natToFΓ(FΓtoNat(k))
    = natToFΓ(alpha)
    = natToFΓ(<X>|m: Arr_Γ_X| F_arrows(m)(k))
    = (<X>|m: Arr_Γ_X| F_arrows(m)(k))<Γ>(id[Γ]) // Instantiate the family on X=Γ
    ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                alpha
    = (   |m: Arr_Γ_Γ| F_arrows(m)(k))(id[Γ])    // Call the function with m=id[Γ]
    =                  F_arrows(id[Γ])(k)        // F is a functor, so it sends identity to the identities (the identity of Prog is the identity program!)
    =                  id(k)
    =                  k
    ```

    - The other direction we need to use naturality.

    **To Be Continued...**

2. For any value `α: Arr[C,D]_IntoΓ_F`, so, a natural transformation $\alpha : \textsf{into}_{\Gamma} \longrightarrow F$, first, we obtain a value `natToFΓ(α)` in the type $F(Γ)$, in this way:
