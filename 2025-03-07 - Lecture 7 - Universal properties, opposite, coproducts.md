# 2025-03-07 - Lecture 7 (Universal properties, opposite, coproducts)



> ## (\*) The second important idea in category theory: universal properties single out specific constructions among all the other ones, in a unique way; for instance, they allow you to talk not about being "a product" but being "the product" without essentially losing any information.

## Theorem ("Being a product" is a universal property)
Take $A,B$ objects. Any two products $P$ and $Q$ of *the same* $A$, $B$ are isomorphic.

*Proof*.

Assume that we are given $P$ and $Q$, such that we know that both of them are products.

*Task objective.* Prove that $P \cong Q$.

*Task objective.* Build two maps $\textsf{p2q} : P \to Q, \textsf{q2p} : Q \to P$, show that $$\textsf{p2q} \,; \textsf{q2p} = \textsf{id}_P, \quad \textsf{q2p} \,; \textsf{p2q} = \textsf{id}_Q.$$

Solution:

Remember that
- $\textsf{fst}_{P} : P \to A$
- $\textsf{snd}_{P} : P \to Q$
- $\textsf{fst}_{Q} : Q \to A$
- $\textsf{snd}_{Q} : Q \to Q$
- $\langle l, r \rangle_P : H \to P$ for any $l : H \to A, r : H \to B$
- $\langle l, r \rangle_Q : H \to Q$ for any $l : H \to A, r : H \to B$

Then, the conversion arrows are the following ones:

$$
\begin{array}{ll}
    \textsf{p2q} := \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q : P \to Q \\
    \textsf{q2p} := \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P : Q \to P \\
\end{array}
$$

We show that $\langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q \,;  \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P = \textsf{id}_{P}$.

$$
\begin{array}{lll}
  & \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q \,;  \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P & \text{(naturality of pairing for $P$)} \\
= & \langle \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q \,; \textsf{fst}_{Q}, \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q \,; \textsf{snd}_{Q} \rangle_P & \text{(pairing equations for $Q$)} \\
= & \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_P & \text{(pair expansion for $P$)} \\
= & \textsf{id}_P & \text{(pair expansion for $P$)}
\end{array}
$$

We show that $\langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P \,;  \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q = \textsf{id}_{Q}$.

$$
\begin{array}{lll}
  & \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P \,;  \langle \textsf{fst}_{P}, \textsf{snd}_{P} \rangle_Q & \text{(naturality of pairing for $Q$)} \\
= & \langle \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P \,; \textsf{fst}_{P}, \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_P \,; \textsf{snd}_{P} \rangle_Q & \text{(pairing equations for $P$)} \\
= & \langle \textsf{fst}_{Q}, \textsf{snd}_{Q} \rangle_Q & \text{(pair expansion for $Q$)} \\
= & \textsf{id}_P & \text{(pair expansion for $Q$)}
\end{array}
$$





# Opposite category

Here is a recipe that given a category gives you another, different, category for free.

Given any category $C$, I construct the following category, which I call $C^\textsf{op}$:
- *(Objects)*: the same objects of $C$,
- *(Arrows)*: I need to choose type `Arr[a][b]` for every `a,b:Obj`. I will chose each arrow type of my new category to be `Arr[b][a]` (note the swap!).

In order to distinguish the choice of arrows, we write `Arr[a][b]` and `Arrop[a][b]`.
- *(Identities)*: Since `Arrop[a][a] = Arr[a][a]`, I can simply *reuse* the original `id[a]: Arr[a][a]`.
- *(Composition)*: I need to give a Rust function
```rust
type Arrop[a][b] = Arr[a][b]

fn compose[a][b][c](f: Arrop[a,b], g: Arrop[b,c]) -> Arrop[a,c] {
    ...
}
fn compose[a][b][c](f: Arr[b,a], g: Arr[c,b]) -> Arr[c,a] {
    compose[c][b][a](g, f)
}
```
- *(Left unitality, right unitality, associativity)*. These all follow from the fact that in my choice of compose I simply used the original compose, which already satisfied these properties.
