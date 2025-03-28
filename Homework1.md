# Homework 1

*Assigned: 28 March 2025*

*Deadline: 28 April 2025*

- Total number of points: **75 points**
- Maximum grade on the homework is obtained with: **60 points**
- Minimum amount of points you need to get: **25 points**

Some points on how to write the homework:

- The more it follows the format and approach of the lectures, the better. You don't have to reinvent anything, just convince me that you understood how to work with the definitions of the course.
- Describe precisely which property you had to use to construct the arrows needed in the various exercise sheet, and give it a name according to the conventions that we used in the course, e.g., $\langle f,g \rangle$ for the pairing arrow if you manage to construct $f$ and $g$, $[f,g]$ for copairing arrow, or $\Lambda(f)$ in the context of exponential objects.
- Of course, you can use all the properties that we have seen during the lecture (for example, you're going to need to use the "naturality of products" for some of these exercises) and also show new ones.

## Exercise 1 (5 points)

Give an example of category with at least 4 objects and at least 8 arrows. You can use Rust to define it in the same way that we have defined previous examples. Define identities, compositions in a precise way. The way in which you justify identity laws and associativity can be less precise.

## Exercise 2 (7 points)

Show that in every category $C$ with all products and a terminal object $1$ there is an arrow $B \times A \to A \times (1 \times B)$. Explicitly construct such arrow, only using the fact that $C$ has all products and terminals.

## Exercise 3 (7 points)

Show that in every category with all coproducts, if there is an arrow $f : A \to B$ there is an arrow $A + A \to B + B$. Explicitly construct such arrow.

## Exercise 4 (9 points)

Show that if $A \cong B$ then $A \times C \cong B \times C$. Do this by explicitly constructing an arrow with signature $A \times C \to B \times C$, an arrow going in the opposite direction, and then show that they compose to the identity in both the ways you can compose them.

## Exercise 5 (10 points)

Show that $A + 0 \cong 0$. (Hint: remember that, as per definition of initial object, any arrow $f : 0 \to H$ is equal to the unique one $! : 0 \to H$ that you have because $0$ is the initial object.)

## Exercise 6 (10 points)

Show that given two arrows $f : A' \to A$ and $g : B \to B'$ then you can construct an arrow $A \Rightarrow B \longrightarrow A' \Rightarrow B'$ (note that there is no mistake in the source and targets of the arrows!)

## Exercise 7.1 (10 points) [Ex. 9.8]

Show that for any objects $A,B,C$ in a CCC there are two arrows
1. $(A\times B)^C \to A^C \times B^C$,
2. $ A^C \times B^C \to (A\times B)^C$.

As usual, describe how you obtained these arrows using which property of the category.

## Exercise 7.2 (10 points) [Ex. 9.8]

Show that, having constructed the two arrows above, they are part of an isomorphism $(A\times B)^C \cong A^C \times B^C$, i.e., they compose to the identity in both cases (you must show *both* compositions to identities!)

## Exercise 8 (7 points)

Consider this type:

```rust
enum MaybeTwice<A> {
    Nothing,
    Just(A),
    TwoResults(A, A, String),
}
```

Consider the functor in `Prog` that sends `X` to `MaybeTwice<X>` defined above.

Define the program on arrows of the functor above (i.e,, the `fmap` of `F`).

Describe what an algebra for the functor essentially amounts to, in the same style as what we saw in the lecture for algebras.
