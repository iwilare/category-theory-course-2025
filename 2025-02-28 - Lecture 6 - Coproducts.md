

# Definition: isomorphisms

Two objects $A$ and $B$ are said to be *isomorphic*, which we write as $A \cong B$, whenever the following thing happens:

You provide two arrows
- $\textsf{a2b} : A \to B,$
- $\textsf{b2a} : B \to A,$

such that the following two equations hold:

$$\textsf{a2b} \,; \textsf{b2a} = \textsf{id}_A, \quad \textsf{b2a} \,; \textsf{a2b} = \textsf{id}_B.$$

## Theorem: being isomorphic is a reflexive notion.

For any object $A$, then $A \cong A$.

## Theorem: being isomorphic is a symmetric notion.

For any object $A,B$, if I know that $A \cong B$, then $B \cong A$.

## Theorem: being isomorphic is a transitive notion.

For any object $A,B,C$, if I know that $A \cong B$ and that $B \cong C$, then I know that $A \cong C$.

# The first important idea in category theory:
## Theorem ("Being a product" is a pairing property) -> Take $A,B$ objects. Any two products $P$ and $Q$ of the same $A$, $B$ are isomorphic.

# Exercise: among the examples that we have seen in the previous lecture, find a category where $A \not \iso A \times A$ and one where $A \iso A \times A$.
