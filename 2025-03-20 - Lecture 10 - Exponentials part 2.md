# 2025-03-21 - Lecture 9 (Distributivity, Universal proprerty of exponentials)

## Today's plan

- Derive the distributivity map using cartesian closed structure
- Prove the uniqueness of exponential objects
- Establish the isomorphism between hom-sets
- Explore additional properties of CCCs

# Distributivity in Cartesian Closed Categories

Last time, we introduced exponential objects and saw how they "internalize" the notion of functions within a category. Today, we'll see how the cartesian closed structure lets us derive important properties - particularly the distributivity map that was previously challenging to construct.

> RECALL Exercise 7.6: Show that in every generic category $C$ that has products and coproducts you can construct an arrow with the following signature:
> $$
> \textsf{dist} : (A \times B) + (A \times C) \longrightarrow A \times (B + C)
> $$

We previously noted that while we can construct the `dist` arrow, we couldn't always find its inverse `opdist` in an arbitrary category with products and coproducts. Today we'll see that in a _cartesian closed_ category, we can not only construct `opdist` but prove that `dist` is actually an isomorphism.

## The Inverse Distributivity Map in a CCC

*Claim:* In a cartesian closed category, we can always construct the inverse distributivity map:
$$
\textsf{opdist} : A \times (B + C) \longrightarrow (A \times B) + (A \times C)
$$

*Proof:*

1. Of course we have these two arrows:
   $$\textsf{inl} : A \times B \longrightarrow (A \times B) + (A \times C)$$
   $$\textsf{inr} : A \times C \longrightarrow (A \times B) + (A \times C)$$
2. Using $\Lambda$ on both of them:
   $$\Lambda_{A,B}(\textsf{inl}) : B \longrightarrow A \Rightarrow ((A \times B) + (A \times C))$$
   $$\Lambda_{A,C}(\textsf{inr}) : C \longrightarrow A \Rightarrow ((A \times B) + (A \times C))$$
3. Note: these two arrows go into the same object! So there is an arrow from the coproduct of the sources:
   $$[\Lambda_{A,B}(\textsf{inl}), \Lambda_{A,C}(\textsf{inr})] : B + C \longrightarrow A \Rightarrow ((A \times B) + (A \times C))$$
4. Add $A$ on the left:
$$\textsf{id}_A \times [\Lambda_{A,B}(\textsf{inl}), \Lambda_{A,C}(\textsf{inr})] : A \times (B + C) \longrightarrow A \times (A \Rightarrow ((A \times B) + (A \times C)))$$
5. Finally, postcompose the arrow with $\textsf{eval}_{A,(A\times B) + (A \times C)}$:
   $$\textsf{eval}_{A,(A\times B) + (A \times C)} : A \times (A \Rightarrow \underbrace{(A\times B) + (A \times C)}_B) \to  \underbrace{(A\times B) + (A \times C)}_B$$
6. The final arrow you obtain is:
   $$(\textsf{id}_A \times [\Lambda_{A,B}(\textsf{inl}), \Lambda_{A,C}(\textsf{inr})])\,;\textsf{eval}_{A,(A\times B) + (A \times C)} : A \times (B + C) \longrightarrow (A \times B) + (A \times C)$$

### Exercise: prove that this is the inverse arrow of $\textsf{opdist}$.

# Lemma: $(f \times g) \,; (f' \times g') = (f \,; f') \times (g \,; g')$

Remember that $f \times g := \langle \textsf{fst} \,; f,  \textsf{snd} \,; g \rangle$.

$$
\begin{array}{rcll}
&   & (f \times g) \,; (f' \times g') & \text{(Definition of $\times$)} \\
& = & \langle \textsf{fst} \,; f, \textsf{snd} \,; g \rangle \,; \langle \textsf{fst} \,; f', \textsf{snd} \,; g' \rangle & \text{(Naturality of pairing)} \\
& = & \langle \langle \textsf{fst} \,; f, \textsf{snd} \,; g \rangle \,; \textsf{fst} \,; f', \langle \textsf{fst} \,; f, \textsf{snd} \,; g \rangle \,; \textsf{snd} \,; g' \rangle & \text{(Product projection laws)} \\
& = & \langle (\textsf{fst} \,; f) \,; f', (\textsf{snd} \,; g) \,; g' \rangle & \text{(Associativity of composition)} \\
& = & \langle \textsf{fst} \,; (f \,; f'), \textsf{snd} \,; (g \,; g') \rangle & \text{(Definition of $\times$)} \\
& = & (f \,; f') \times (g \,; g')
\end{array}
$$


# Lemma: $\textsf{id}_A \times \textsf{id}_B = \textsf{id}_{A \times B}$

In other words, putting the operation that does nothing in parallel is the same as just doing nothing, in general.

$$
\begin{array}{rcll}
&   & \textsf{id}_A \times \textsf{id}_B & \\
& = & \langle \textsf{fst} \,; \textsf{id}_A, \textsf{snd} \,; \textsf{id}_B \rangle & \text{(Definition of $\times$ for arrows)} \\
& = & \langle \textsf{fst}, \textsf{snd} \rangle & \text{(Lemma in Lecture 6)} \\
& = & \textsf{id}_{A \times B} &  \\
\end{array}
$$

# Lemma: $\Lambda(\textsf{eval}) = \textsf{id}_{A \Rightarrow B}$

$$
\begin{array}{rcll}
&   & \Lambda(\textsf{eval}) & \text{(Identity law for categories)} \\
& = & \Lambda(\textsf{id}_{(A \Rightarrow B) \times A} \,; \textsf{eval}) & \text{(Lemma above)} \\
& = & \Lambda((\textsf{id}_{A \Rightarrow B} \times \textsf{id}_{A}) \,; \textsf{eval}) & \text{(Lambda expansion for $h := \textsf{id}_{A \Rightarrow B}$)}  \\
& = & \textsf{id}_{A \Rightarrow B} &  \\
\end{array}
$$


# Uniqueness of Exponential Objects

*Claim:* If $A \Rightarrow B$ and $A \Rightarrow' B$ are both exponential objects from $A$ to $B$, then $A \Rightarrow B \cong A \Rightarrow' B$.

*Proof:*

Since $A \Rightarrow B$ is an exponential object, it comes with:
- $\textsf{eval} : (A \Rightarrow B) \times A \to B$
- For any object $H$ and arrow $f : H \times A \to B$, an arrow $\Lambda(f) : H \to (A \Rightarrow B)$
- Lambda equation, $\eta$-uniqueness.

Similarly, $A \Rightarrow' B$ has:
- $\textsf{eval}' : (A \Rightarrow' B) \times A \to B$
- For any object $H$ and arrow $f : H \times A \to B$, an arrow $\Lambda'(f) : H \to (A \Rightarrow' B)$
- Lambda equation, $\eta$-uniqueness.

Now, let's define:

- $\textsf{E2E'} := \Lambda'(\textsf{eval}) : (A \Rightarrow B) \to (A \Rightarrow' B)$
- $\textsf{E'2E} := \Lambda(\textsf{eval}') : (A \Rightarrow' B) \to (A \Rightarrow B)$

We need to show that $\textsf{E'2E} \,; \textsf{E2E'} = \textsf{id}_{(A \Rightarrow' B)}$ and $\textsf{E2E'} \,; \textsf{E'2E} = \textsf{id}_{(A \Rightarrow B)}$.

For $\textsf{E'2E} \,; \textsf{E2E'}$:

1. First, by the *Lambda expansion* property of $\Lambda'$, we know that for any arrow $h : H \to (A \Rightarrow' B)$:
   $$\Lambda'((h \times \textsf{id}_A) \,; \textsf{eval}') = h$$

2. Let's take $h = \textsf{E'2E} \,; \textsf{E2E'}$ (so we picked $H := (A \Rightarrow' B)$):

   $$\Lambda'(((\textsf{E'2E} \,; \textsf{E2E'}) \times \textsf{id}_A) \,; \textsf{eval}') = \textsf{E'2E} \,; \textsf{E2E'}$$

   Hence:

   $$
   \begin{array}{rcll}
    \textsf{E'2E} \,; \textsf{E2E'} & = & \Lambda'(((\textsf{E'2E} \,; \textsf{E2E'}) \times \textsf{id}_A) \,; \textsf{eval}') & \text{(Functoriality of $f\times g$)} \\
    & & \Lambda'((\textsf{E'2E} \times \textsf{id}_A) \,; (\textsf{E2E'} \times \textsf{id}_A) \,; \textsf{eval}') & \text{(Definition of \textsf{E2E'})} \\
    & & \Lambda'((\textsf{E'2E} \times \textsf{id}_A) \,; (\Lambda'(\textsf{eval}) \times \textsf{id}_A) \,; \textsf{eval}') & \text{(Lambda equation for $\Lambda'$)} \\
    & & \Lambda'((\textsf{E'2E} \times \textsf{id}_A) \,; \textsf{eval}) & \text{(Definition of $\Lambda'$)} \\
    & & \Lambda'((\Lambda(\textsf{eval}') \times \textsf{id}_A) \,; \textsf{eval}) & \text{(Lambda equation for $\Lambda$)} \\
    & & \Lambda'(\textsf{eval}') & \text{(Previous lemma)}\\
    & & \textsf{id}_{A\Rightarrow' B} \\
   \end{array}
   $$

A symmetric argument shows that $\textsf{E2E'} \,; \textsf{E'2E} = \textsf{id}_{(A \Rightarrow B)}$.


# The Product-Exponential Isomorphism

For any cartesian closed category $C$, which as types of arrows `Arr_A_B`, there's a fundamental isomorphism in the category $\text{Prog}$ (!!) between the following two types:

$$\texttt{Arr\_HxA\_B} \cong \texttt{Arr\_H\_A⇒B} $$

We describe the intuition using the algebraic perspective.
- *(Curry.*) Given an arrow $h : H \times A \to B$, I give you an arrow $H \to A \Rightarrow B$. This is exactly what $\Lambda$ does, let's call it $\textsf{curry}$ instead.
- *(Uncurry.*) Given an arrow $f : H \to A \Rightarrow B$, I give you an arrow $H \times A \to B$.
I do this composition:

$$
\begin{array}{llll}
& f & : & H \to A \Rightarrow B \\
& f \times \textsf{id}_A & : & H \times A \to (A \Rightarrow B) \times A \\
\textsf{uncurry}(f) := & (f \times \textsf{id}_A) \,; \textsf{eval} & : & H \times A \to B \\
\end{array}
$$

In Rust, this isomorphism is precisely given by:

```rust
fn curry(h: Arr_HxA_B) -> Arr_H_A⇒B {
  lambda_h // this is the Λ(f) of category theory
}

fn uncurry(f: Arr_H_A⇒B) -> Arr_HxA_B {
  compose(parallel_f_id,eval)
}
```

Why do these form an isomorphism in $\text{Prog}$?
- For any $f : H \times A \to B$,
  $$
  \begin{array}{lll}
      & \textsf{uncurry}(\textsf{curry}(f)) & \text{(Defintion of curry)}\\
    = & \textsf{uncurry}(\Lambda(f)) & \text{(Defintion of uncurry)} \\
    = & \Lambda(f) \times \textsf{id}_A \,; \textsf{eval} & \text{(Lambda equation)} \\
    = & f & \\
    = & \textsf{id}(f) & \\
  \end{array}
  $$
  Therefore $\textsf{curry} \,; \textsf{uncurry} = \textsf{id}$.
- For any $h : H \to A \Rightarrow B$,
  $$
  \begin{array}{lll}
      & \textsf{curry}(\textsf{uncurry}(f)) & \text{(Defintion of curry)}\\
      & \Lambda(\textsf{uncurry}(f)) & \text{(Defintion of uncurry)}\\
      & \Lambda((f \times \textsf{id}_A)\,;\textsf{eval}) & \text{(Lambda expansion)}\\
      & f & \\
    = & \textsf{id}(f) & \\
  \end{array}
  $$
  Therefore $\textsf{uncurry} \,; \textsf{curry} = \textsf{id}$.

> Remember! This works for ANY category. Prog is mentioned essentially because categories use *types* in their definition.
>
> Now, and only now, we show how the above definitions instantiate in Prog:

## Example in Prog

In the category $\text{Prog}$ the above operations are defined as follows:

```rust
// A function of two arguments
fn f(p: Pair<H, A>) -> B { ... }

// Its curried version
fn curry_f(h: H) -> Func<A, B> {
    |a: A| f(Pair { first: h, second: a })
}

// And uncurrying
fn uncurry_g(p: Pair<H, A>) -> B {
    (g(p.first))(p.second)
}
```

# Exercises

In the following $B^A$ is the exponential of $A$ and $B$, $0$ is the initial object, $1$ is the terminal object for some category $C$ that you do not know anything about.

## Exercise 9.1
In a CCC, show that there is an isomorphism $A^1 \cong A$.

## Exercise 9.2
In a CCC, show that there is an isomorphism $1^A \cong 1$.

## Exercise 9.3
In a CCC, show that there is an isomorphism $A^{0} \cong 1$.

## Exercise 9.4
In a CCC, show that there is an isomorphism $0^A \cong 0$.

## Exercise 9.5
In a CCC, show that there is an isomorphism $A^{1+1} \cong A \times A$.

## Exercise 9.6

In a CCC, show that there is an isomorphism $(A^B)^C \cong A^{B \times C}$.

## Exercise 9.7
In a CCC, show that there is an isomorphism $A^{B+C} \cong A^B \times A^C$.

## Exercise 9.8

In a CCC, show that there is an isomorphism $(A\times B)^C \cong A^C \times B^C$.
