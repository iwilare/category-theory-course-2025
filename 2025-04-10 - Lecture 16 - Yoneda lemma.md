# 2025-04-10 - Lecture 15 (Natural transformations 2, Yoneda)

# Working with natural transformations

Let's see some more or less abstract definitions that work with natural transformations.

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

Then, I can cook you up a new natural transformation $\gamma : F \longrightarrow H$, which I write like as $\alpha \,; \beta$ (note: this is NOT a composition of any category yet. For now it's just notation.)

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
(\alpha \ast \beta)_X &:=& G(\alpha_X) \,; \beta_{F'(X)}  \\
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
\phantom{F(f)} G(F(X)) & \xrightarrow{\phantom{ii} (\alpha \ast \beta)_X \phantom{ii}} & G'(F'(X)) \phantom{F(f)} \\
G(F(f)) \downarrow & \raisebox{2pt}{\tiny =} & \downarrow G'(F'(f)) \\
\phantom{F(f)} G(F(Y)) & \xrightarrow{\phantom{ii} (\alpha \ast \beta)_Y \phantom{ii}} & G'(F'(Y)) \phantom{F(f)} \\
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

I cook up a functor $\textsf{from}_\Gamma : C \to \text{Prog}$.

- *(Program on objects.)* Given an object $X$ of $C$, I send it to the object of $\text{Prog}$ type `Arr_Γ_X`. So we define `fromΓ(X) := Arr_Γ_X`.
- *(Program on arrows.)* This is what the program on arrows does:

```rust
fn fromΓ_arrows(f: Arr_X_Y) -> ProgArr_fromΓ(X)_fromΓ(Y) {
    "fn fromΓ_arrows_f(p: Arr_Γ_X) -> Arr_Γ_Y {
        compose(p, f)
    }"
}
```

Keep this in mind for the next section: effectively, if we think of programs and function types as being the same thing...

```rust
// *important for later!!*
fromΓ_arrows(f)(p) = compose(p, f)
```

- *(Respects identities)*
    ```rust
    "fn fromΓ_arrows_f(p: Arr_Γ_X) -> Arr_Γ_X {
        compose(p, id)
    }"
    // this is equivalent to... the identity arrow on Prog!
    // so when f=identity I send it to the identity on Prog.
    "fn fromΓ_arrows_f(p: Arr_Γ_X) -> Arr_Γ_X {
        p
    }"
    ```
- *(Respects compositions)* Same story.

# Yoneda's lemma

The most celebrated and pervasive result of category theory.

Take a functor $F : C \Rightarrow \text{Prog}$. Take an object $\Gamma$ of $C$.

So:
- $F$ is a functor $C$ to $\text{Prog}$.
- $\textsf{from}_\Gamma$ is a functor $C$ to $\text{Prog}$.

Realisations:

- There is a type $F(\Gamma)$.
- Take the type `Arr[C,Prog]_fromΓ_F`, the type of arrows in the category $[C,\text{Prog}]$ that go from the functor $\textsf{from}_\Gamma$ to the functor $F$: this was defined to be the type that has a value for each possible natural transformation $\alpha : \textsf{from}_\Gamma \longrightarrow F$.

*(Yoneda's lemma.)* the two types above are isomorphic in $\text{Prog}$: $$\texttt{Arr[C,Prog]\_fromΓ\_F} \cong F(\Gamma).$$

*In other words,* **There is a one-to-one correspondence between values of $F(\Gamma)$ and natural transformations with the signature $\alpha : \textsf{from}_\Gamma \longrightarrow F$.** (this is what it means to be isomorphic as types/objects of $\text{Prog}$.)

*Proof.*

We need to create an isomorphism in Prog, so we need to create two programs in Rust.

- $\texttt{natToFΓ} : \texttt{Arr\_fromΓ\_F} \to F(\Gamma)$

```rust
fn natToFΓ(α: Arr[C,Prog]_fromΓ_F) -> F(Γ) {
    // Recall:
    //   α is a natural transformation from fromΓ to F.
    //   so, it's a polymorphic function like this:
    //
    //   fn α<X>(a: fromΓ(X)) -> F(X) { ... }
    //   fn α<X>(a: Arr_Γ_X) -> F(X) {
    //      ...
    //   }
    //
    //   which moreover is natural, i.e., makes the square commute

    // In particular,
    //      α<Γ> : Arr_Γ_Γ -> F(Γ)
    // is a perfectly valid concrete program.

    // So I can call this program on the special value id of Γ.

    α<Γ>(id[Γ])
}

fn FΓtoNat(k: F(Γ)) -> Arr[C,Prog]_fromΓ_F {
    // So, here I am given a value k of type F(Γ)
    // and I need to construct a natural transformation, i.e., a parametric function:
    //
    //   fn α<X>(f: fromΓ(X)) -> F(X) { ... }
    //   fn α<X>(a: Arr_Γ_X) -> F(X) {
    //      ...
    //   }
    //
    // (remember how fromΓ is defined: on X, it gives me the type Arr_Γ_X)
    // and this family must also be natural, i.e., make the square commute.

    // Key point: F is a functor.
    fn α<X>(m: Arr_Γ_X) -> F(X) {
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
    α
}
```

Without comments:
```rust
fn natToFΓ(α: Arr[C,Prog]_fromΓ_F) -> F(Γ) {
    α<Γ>(id[Γ])
}
fn FΓtoNat(k: F(Γ)) -> Arr[C,Prog]_fromΓ_F {
    fn α<X>(m: Arr_Γ_X) -> F(X) {
         F_arrows(m)(k)
    }
    α
}
```

**Key sentence: in natToFΓ, we take an `α` an instantiate it on the identity of `Γ`.**

**Key sentence: in FΓtoNat, `α` takes an arrow `m` and "transports" `k` along that arrow, using the program on arrows of $F$.**

We have to check that the specific `α` given by `FΓtoNat` really is a natural family of arrows of `Prog`:

- For every arrow $f : X \to Y$ in $C$, this square *AGAIN in our beloved category $\text{Prog}$* commutes:
    $$
    \begin{array}{ccccccccc}
    \phantom{\textsf{from}_\Gamma(f)} \textsf{from}_\Gamma(X) & \xrightarrow{\phantom{iii}\alpha_X\phantom{iii}} & F(X) \phantom{F(f)} & \\
    {\textsf{from}_\Gamma(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {F(f)}\\
    \phantom{\textsf{from}_\Gamma(f)} \textsf{from}_\Gamma(Y) & \xrightarrow{\phantom{iii}\alpha_Y \phantom{iii}} & F(Y) \phantom{F(f)} \\
    \end{array}
    $$

Let's write this down as program equivalence:

(The `k: F(Γ)` is fixed, we're checking inside of the definition of `FΓtoNat` that this α is indeed a natural transformation.)

For every value of `m: fromΓ(X) = Arr_Γ_X`, so $m : Γ \to X$ is an arrow in $C$,

```
    F_arrows(f)(α<X>(m))
  = α<Y>(fromΓ_arrows(f)(m))
```

This is the general definition of naturality for our specific α that we know how it's defined.

Start from the top:

```rust
    F_arrows(f)(α<X>(m))    // Definition of α<X>
  = F_arrows(f)(F_arrows(m)(k))
```

Start from the bottom:

```rust
  = α<Y>(fromΓ_arrows(f)(m))  // Definition of fromΓ_arrows
  = α<Y>(compose(m, f))       // Just for simplicity take z=compose(m, f),
  = α<Y>(z)                   // Definition of alpha,
  = F_arrows(z)(k)            // z=compose(m, f),
  = F_arrows(compose(m, f))(k) // F is a functor, so it sends compositions to compositions
    ^^^^^^^^^^^^^^^^^^^^^^^
  = compose_(F_arrows(m))_(F_arrows(f))(k) // Remember, compose in Prog swaps the two arguments.
  = F_arrows(f)(F_arrows(m)(k))
```

So, we obtained the same thing! So we conclude that the `α` we gave really is a natural transformation.

---

**Take a small break and let's think about what it means for an $\alpha$ with that signature to be natural for a second.** This will be important in the second part of the proof.

Whenever we have a generic $\alpha : \textsf{from}_\Gamma : F$, the naturality equation looks like this: for every $f : \Gamma \to X$ and $m : X \to Y$,

```rust
    F_arrows(m)(α<X>(f))      // Definition of naturality square,
  = α<Y>(fromΓ_arrows(m)(f))  // Definition of fromΓ_arrows,
  = α<Y>(compose(f, m))
```

So, `F_arrows(m)(α<X>(f)) = α<Y>(compose(f, m))`.

Practically: if I use the program on arrows of $F$ onto $\alpha_X(f)$ I can simply move $m$ inside, and get rid of `F_arrows`.

---

**Now, we need to check that `natToFΓ` and `FΓtoNat` really compose to the identity in both ways, so they form an isomorphism.**

Again, we have to reason by program equivalence:

1. For any value `k: F(Γ)`, we need to check that:

    ```rust
    natToFΓ(FΓtoNat(k)) = k
    ```

    The natural transformation given by `FΓtoNat(k)` is by definition this one:

    ```rust
    fn FΓtoNat(k: F(Γ)) -> Arr[C,Prog]_fromΓ_F {
        fn α<X>(m: Arr_Γ_X) -> F(X) {
            F_arrows(m)(k)
        }
        α
    }
    ```

    What does `natToFΓ`? It takes the `α` and runs it on the identity.

    ```rust
    fn natToFΓ(α: Arr[C,Prog]_fromΓ_F) -> F(Γ) {
        α<Γ>(id[Γ])
    }
    ```

    So,

    ```rust
      natToFΓ(FΓtoNat(k))
    = natToFΓ(α)
    = natToFΓ(<X>|m: Arr_Γ_X| F_arrows(m)(k))
    = (<X>|m: Arr_Γ_X| F_arrows(m)(k))<Γ>(id[Γ]) // Instantiate the type parameter X with Γ
      ^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^^
                α
    = (   |m: Arr_Γ_Γ| F_arrows(m)(k))(id[Γ])    // Call the function with m=id[Γ]
    =                  F_arrows(id[Γ])(k)        // F is a functor, so it sends identity to
                                                 // identity (the identity of Prog
                                                 // is the identity program!)
    =                  id(k)
    =                  k
    ```

2. For any value `β: Arr[C,Prog]_fromΓ_F`, so, a natural transformation $\beta : \textsf{from}_{\Gamma} \longrightarrow F$,
   ```
    FΓtoNat(natToFΓ(β)) = β
   ```
   (*) Keep in mind: what does it mean for two natural transformations $\alpha,\beta : F \longrightarrow G$ to be equal? (for some $F,G:C \Rightarrow D$) It means that for each $X$, the two arrows $\alpha_X = \beta_X$ (these are arrows of $D$) are the same values in the type of arrows `Arr_F(X)_G(X)`.

   First, we obtain a value `natToFΓ(β)` in the type $F(Γ)$, in this way:

   ```rust
    let k: F(Γ) = natToFΓ(β)
                = β<Γ>(id[Γ]) // natToFΓ takes the β and instantiates X=Γ and calls it on the identity
   ```

   Then, `FΓtoNat` does something specific: it takes this value `k`, and gives me back this specific natural transformation here:
    ```rust
    // given some generic k above,
    fn α<X>(m: Arr_Γ_X) -> F(X) {
        F_arrows(m)(k)
    }
    // which in this case,
    fn α<X>(m: Arr_Γ_X) -> F(X) {
        F_arrows(m)(β<Γ>(id[Γ]))
    }
    ```
    The question is whether this new natural transformation that we obtained really is the same as the one we started with.
    Using (*), we need to check that for every `<X>` the two resulting arrow we obtain are the same. But the target category $D$ here is $\text{Prog}$, so equality of arrows is again determined by program equivalence!

    So: to check

    ```rust
    α = β         // as arrows of [C,Prog]
    ```

    as arrows of the functor category $[C,\text{Prog}]$, we need to check that for every $X$ of $C$ these two *arrows of $\text{Prog}$* are the same:

    ```rust
    α<X> = β<X>   // as arrows of Prog
    ```

    But since equality of arrows in $\text{Prog}$ is program equivalence, we need to show that for every `m:Arr_Γ_X`, so $m : \Gamma \to X$ which we don't know anything about, these two are the same value in the type `F(X)`:

    ```
    α<X>(m) = β<X>(m)
    ```

    Now, let's unfold how things are defined and continue by program equivalence:

    First: remember what we did before by looking at what naturality means for our generic `β`, which we know is natural:

    > For any $f : \Gamma \to X$ and $m : X \to Y$,
    >
    > ```rust
    >   F_arrows(m)(β<X>(f))
    > = β<Y>(compose(f, m))
    > ```

    Now, let's see what happens!

    ```rust
      α<X>(m)                   // Definition of α,
    = F_arrows(m)(β<Γ>(id[Γ]))  // Using the above naturality of β, for X=Γ, f=id, m=m
    = β<X>(compose(id, m))      // Identities in categories,
    = β<X>(m)                   // Done!
    ```

# The Yoneda embedding

Let's see what the Yoneda lemma becomes when we make a special choice of $F$: using $\textsf{from}_Γ$ again!

Remember, Yoneda works for any functor $F : C \Rightarrow \text{Prog}$.

Now we pick as our $F := \textsf{from}_Δ$. Yoneda now says that

Before:
$$\texttt{Arr[C,Prog]\_fromΓ\_F} \cong F(Γ)$$
After:
$$\texttt{Arr[C,Prog]\_fromΓ\_fromΔ} \cong \textsf{from}_Δ(Γ)$$

$$\texttt{Arr[C,Prog]\_fromΓ\_fromΔ} \cong \texttt{Arr\_Δ\_Γ}$$

...What is this saying?

**That in the functor category $\text{[C,Prog]}$ there is literally a faithful copy of the category $C$. Same exact objects, same exact arrows.**

If we instead do the same game but with $C:=C^{\textsf{op}}$ and use $\textsf{from}_\Gamma$ as a functor $[C^{\textsf{op}}, \text{Prog}]$ (which, as a functor from $C^{\textsf{op}}$, we will call $\textsf{into}_\Gamma$: it's defined in the same way, we just picked the category $C$ that we do this game for to be $C^\textsf{op}$ instead)


We always have two functors for any $C$:

1. $\textsf{from}_\Gamma : C \Rightarrow \text{Prog}$,
1. $\textsf{into}_\Gamma : C^\textsf{op} \Rightarrow \text{Prog}$.

We obtain this:

$$\texttt{Arr[Cᵒᵖ,Prog]\_intoΓ\_intoΔ} \cong \texttt{Arr\_Γ\_Δ}$$

# The Yoneda embedding

The act of sending $Γ$ to $\textsf{from}_Γ$ is also a functor! But there is a catch... it's (almost) a functor of this shape:

$$よ : C \to [C,\textsf{Prog}]$$

On objects, it sends $\Gamma \mapsto \textsf{from}_\Gamma$, but on arrows you're not doing to be able to define it:
What I mean is that if someone gives you an arrow $f : \Gamma \to \Delta$, you are supposed to craft a natural transformation $よ\_\texttt{arrows}(f) : \textsf{from}_\Gamma \longrightarrow \textsf{from}_\Delta$. This is not possible!!

*(Claim. Check this.*) You *can* do it if $f : \Delta \to \Gamma$ instead, in the opposite direction.

So, really, this functor *can* defined if you give it this shape:

$$よ : C^{\textsf{op}} \to [C,\textsf{Prog}]$$

There has to be an $^\textsf{op}$ on the category of the left, otherwise things do not work.

How to define this functor?
- *(Program on objects.)* I send an object $Γ$ of $C$ to an object of $[C,\textsf{Prog}]$, the functor $\textsf{from}_Γ$.
- *(Program on arrows.)* Given an arrow $f : \Gamma \to \Delta$ of $C^\textsf{op}$, I need to give an arrow in the category $[C,\textsf{Prog}]$ from the object $\textsf{from}_Γ$ to the object $\textsf{from}_\Delta$.
   - What is an arrow $f : \Gamma \to \Delta$ of $C^\textsf{op}$? It's (secretly!!) an arrow $f : \Delta \to \Gamma$ of $C$.
   - What is an arrow from the object $\textsf{from}_Γ$ to the object $\textsf{from}_\Delta$ of the category $[C,\textsf{Prog}]$? It's (not so secretly!!) a natural transformation $\alpha : \textsf{from}_\Gamma \longrightarrow \textsf{from}_\Delta$. *Exercise:* check that we can indeed construct such a family of arrows which is also natural.

We will see in the next lecture that secretly $よ$ is the curried version of a very important functor:
$$\text{Arr} : C^\textsf{op} \times C \to \textsf{Prog}$$

# Beware of fake Yoneda lemmas!

People often can find online explanations of the Yoneda lemma using Haskell.

*(Claim.)* Those explainations using standard programming languages (i.e., not math nor the presentation given here) are *NOT* about the Yoneda lemma.

Typically, in those explainations Haskell is used and the Yoneda lemma is presented as saying something like this:

### Fake Yoneda lemma, in Haskell

Given any Functor `F` and a type `a` there is an isomorphism of types between `∀ x. (a -> x) -> F x` and `F a`.

Here are some problems with this definition.

- In our course we have never mentioned `∀` as a part of our language, it's just part of what our types can be: we *do* use parametric functions and this effectively amounts to a sort of "∀", but this quantification is not part of the types themselves (it just never appears). Modeling what it means for a programming language to have this sort of "internal ∀" (which for example allows you to write something like `(∀ x. x -> x) -> Bool`) is actually extremely complicated!

   *(Claim.)* Consider the category where objects are the types of such a programming language with "internal ∀" and arrows are the programs writable in such a programming language, and equality of arrows is defined to be program equivalence in this programming language. Then, there is no fully-faithful functor (i.e., a functor such that programs on arrows are isomorphisms of types) from such a category into $\textrm{Prog}$. Intuition: there are no "standard/easy models" of this programming language. References for this extra fact: [^1][^2][^4]
- The kind of reasoning that one uses in Haskell is very informal, it's just not program equivalence, because you need to pull out of thin air the "naturality" property that we have always *assumed* to have; in those tutorial naturality is taken implicitly as a consequence of something called "parametricity", a (very nice but) much more complicated property to justify. Take a look at [^3] for the concept of parametricity.
- A technical condition that we do not mention for the Yoneda lemma is that $C$ must be a *small category*. What this means exactly is out of the scope of the course, but intuitively, is that every category that we can define inside Rust (i.e. that we can literally write the objects and arrows types for) is a small category. The rest is typically not small. Examples of categories that are not small:

    - $\textrm{Prog}$ is not small,
    - $\textrm{Cat}$ is not small.

    If $C,D$ are small categories, $[C,D]$ is a small category.
- With this in mind, the Yoneda lemma presented in Haskell is essentially picking $C$ to be $\textrm{Prog}$ (because `a` is a type, i.e., a value in the type of objects $\text{Prog}$), and so the Yoneda lemma in Haskell says something about functors $[\textrm{Prog},\textrm{Prog}]$.

#### References

[^1]: Reynolds, "Polymorphism is not set-theoretic" (1984)

[^2]: Pitts, "Polymorphism is set theoretic, constructively" (1987)

[^3]: Wadler, "Theorems for Free!" (1989)

[^4]: Bainbridge et al. "Functorial polymorphism" (1990)
