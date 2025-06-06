# 2025-04-25 - Lecture 18 (More functors, monoidal categories)

Today we will see how to build new functors from old ones.

## Claim: if $D$ has products, then $[C,D]$ has products.

What does this mean?

Imagine I am given two objects of $[C,D]$, i.e., two functors $F, G : C \Rightarrow D$ with the same source and target category.

If we want to show that $[C,D]$ has products, then we need to create a new object $P$ of this category and show that it is indeed the product of $F$ and $G$.

Let's build a new functor $F \times G$:

- *(Program on objects)*. We need to send an object $C$ to an object in $D$. We choose this:
```rust
fn F×G_obj(a: C.Obj) -> D.Obj {
  F_obj(a) × G_obj(a)
}
```

- *(Program on arrows)*. Given `a,b: C.Obj`

```rust
fn F×G_arrows[a][b](f: C.Arr[a][b]) -> D.Arr[F×G_obj(a), F×G_obj(b)] {
  F_obj(f) × G_obj(f)
}
```

where `F_obj(f) × G_obj(f)` is the "parallel arrow" we defined many lectures ago.

Now, we need to show that this is indeed a product. So we need to construct two arrows
- $\textsf{fst} : F \times G \longrightarrow F$,
- $\textsf{snd}  : F \times G \longrightarrow G$,

as arrows of $[C,D]$. But what are these? Arrows of $[C,D]$ are natural transformations!

So, we build a natural transformation:

$$\alpha : F \times G \longrightarrow F$$

given by a family of arrows for each $X$ an object of $C$,

$$\alpha_X : (F \times G)(X) \longrightarrow F(X)$$
$$\alpha_X : F(X) \times G(X) \longrightarrow F(X)$$

what arrow can we pick here? We can pick $\textsf{fst} : A \times B \to A$ for $A = F(X)$ and $B = G(X)$ is an arrow, which we have because $D$ has all products.

But now we have to make sure that this is indeed a natural transformation.

So, what this means is that for every $f : X \to Y$:

$$
\begin{array}{ccccccccc}
\phantom{(F \times G)(f)} (F \times G)(X) & \xrightarrow{\phantom{iii}\alpha_X\phantom{iii}} & F(X) \phantom{F(f)} & \\
{(F \times G)(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {F(f)}\\
\phantom{(F \times G)(f)} (F \times G)(Y) & \xrightarrow{\phantom{iii}\alpha_Y \phantom{iii}} & F(Y) \phantom{F(f)} \\
\end{array}
$$

$$
\begin{array}{ccccccccc}
\phantom{F(f) \times G(f)} F(X) \times G(X) & \xrightarrow{\phantom{iii}\textsf{fst}\phantom{iii}} & F(X) \phantom{F(f)} & \\
{F(f) \times G(f)} \downarrow & \raisebox{2pt}{\tiny =} & \downarrow {F(f)}\\
\phantom{F(f) \times G(f)} F(Y) \times G(Y) & \xrightarrow{\phantom{iii}\textsf{fst} \phantom{iii}} & F(Y) \phantom{F(f)} \\
\end{array}
$$

Equationally, I need to show that

$$
\begin{array}{rcll}
   &   & (F(f) \times G(f)) \,; \textsf{fst} & \text{(unfolding the parallel pair of arrows)} \\
& = & \langle \textsf{fst} \,; F(f), \textsf{snd} \,; G(f) \rangle \,; \textsf{fst} & \text{(product equation)} \\
 & = & \textsf{fst}\,;F(f)
\end{array}$$

This is only part of the things we have to do in order to make sure that $[C,D]$ has products, but it's very long to check all of this.

## Claim: if $D$ has coproducts, then $[C,D]$ has coproducts.

Do everything but with $D^\textsf{op}$.

## Claim: if $D$ has exponential objects, then it's not enough.

You need quite more extra structure on $D$ to be able to say that the functor category $[C,D]$ is closed.

We need: $C$ small + something called "completeness".

Main example: $[C,\text{Prog}]$ turns out to have exponential objects, but they are not described as easily.

Spoiler: https://ncatlab.org/nlab/show/cartesian+closed+category#exponentials_of_cartesian_closed_categories

# Natural isomorphism of functors

Two functors $F,G : C \Rightarrow D$ are said to be "naturally isomorphic" when they are isomorphic as objects of $[C,D]$. Note that this is not the same as $F$ and $G$ being equal?

**Drawing on the whiteboard.**



# Small change of topic.





# Example: the $\times : C \times C \Rightarrow C$ functor

Take any category $C$ that has all products. I build you the "functor that gives you the product".

1. *(Program on objects.)* I need to build a program that, given an object of the category $C \times C$, i.e., a value in the type `Pair<C.Obj, C.Obj>`, gives you an object of the category $C$.

    So, given such a value `(A,B): Pair<C.Obj, C.Obj>`, for `A: C.Obj`, `B: C.Obj`, I give you the object $A \times B$, which is an object of our category because we assumed that $C$ has all products. (Technical note: we have to *choose* a specific object which satisfies the property of being a product, of which there could be many; but we know that they are all isomorphic, so it does not really matter which one we pick.)

2. *(Program on arrows.)* The program on arrow is essentially this:

```rust
fn ×_arrows[x][y](f: C×C.Arr[x][y]): C:Arr[F_obj(x),F_obj(y)] { ... }
```

where `x,y` are two values `x,y: C×C.Obj`, and `F_obj` is the assignment we defined above sending two objects to their product.

but since we defined `C×C.Obj = Pair<C.Obj, C.Obj`, then we have two values `x,y: Pair<C.Obj, C.Obj>`,

and since `x,y` are pairs, we basically have two values `(a,b), (c,d): Pair<C.Obj, C.Obj>`, for `a,b,c,d:C.Obj`,

```rust
fn ×_arrows[a][b][c][d](f: C×C.Arr[(a,b)][(c,d)]): C:Arr[ a × b, b × d, ] { ... }
```

but since we defined the arrows of `C×C.Arr` to be precisely pairs of arrows,

```rust
fn ×_arrows[a][b][c][d](f: C.Arr[a][b], g: C.Arr[c][d]): C.Arr[a × b][c × d] {
  f × g
}
```

given an arrow $(A,B) \to (C,D)$ in the product category, i.e., two arrows $f : A \to C$ and $g : B \to D$, I need to build an arrow $A \times B \to C \times D$. Look! Here we can give exactly the definition of $f \times g$ that we defined many lectures ago: this is the "correct" one.

But now... we also have to check functoriality!

1. ($F$ sends identities to identities): this amounts to check that $$\textsf{id}_A \times \textsf{id}_B = \textsf{id}_{A \times B}$$ which is one of the properties we checked back in the day!

2. ($F$ sends compositions to compositions): imagine we are given two composable arrows in `C×C`, this means two arrows $k : (A,B) \to (A',B')$ (i.e., a pair of arrows $k = (f,g)$) and $m : (A',B') \to (A'',B'')$ (i.e., a pair of arrows $m = (f',g')$), then this "property" of the functor means we must check that composing their result with $\times$ is the same as using $\times$ with the composition.

    this amounts to check that $$(f \times g) \,; (f' \times g') = (f \,; f') \times (g \,; g')$$ which is one of the properties we checked back in the day!

We checked both of these properties in Lecture 10 about exponentials.

# Example: the $+ : C \times C \Rightarrow C$ functor

Same thing as above, but for coproducts.

# Takeaway message

As you saw, there some cases where you have a functor $C \times C \Rightarrow C$ on your category. It turns out that there are some more examples which do *not* come from product/coproduct structure.

Moreover, observe that these functors so far behave very well with respect to other specific objects:

- $1 \times X \cong X$.
- $X \times 1 \cong X$.
- $0 + X \cong X$.
- $X + 0 \cong X$.
- $X \times (Y \times Z) \cong (X \times Y) \times Z$.
- $X + (Y + Z) \cong (X + Y) + Z$.

But, again, where $1$ and $0$ are the terminal and initial objects. Turns out, there are certain objects (without a universal property!!) with respect to the functors with shape $C \times C \Rightarrow C$ are well-behaved.

# Example: the preorder $(\N,\le)$

- The product in this category was $\min$
- The coproduct in this category was $\max$
- We know automatically that there is a functor $(\N,\le) \times (\N,\le) \to (\N,\le)$. Why?

Because if $n \le n'$ and $m \le m'$ then $\min\{n,m\} \le \min\{n',m'\}$.

- A reasonable guess for what it means to be the "product" would have been for example $*$ the operation that multiplies numbers together.
- A reasonable guess for what it means to be the "coproduct"(sum) would have been for example $+$ the operation that sums two numbers together.

Take the program that does *addition* of two numbers (the literal addition!) and the program that computes the *multiplication* of two numbers: these two operations *still* assemble into functors with the same shape as $\times$ and $+$.

### Claim

1. There is a functor $\oplus : (\N,\le) \times (\N,\le) \to (\N,\le)$ defined by sending $(a,b) \mapsto a + b$ their addition. Now, we need to check that this really is a functor:

   If I know that $$(n,m) \le (n',m'),$$ which by definition of product category means that$$n \le m \text{ and } n' \le m',$$ then I need to show that $$F(n,m) \le F(n',m'),$$ which by definition of our functor $F$ in this is case means that $$n + m \le n' + m'$$ which is true because of a general property of natural numbers which we take as intuitive.

   So, to summarize, the program on arrows is this one: if $n \le n'$ and $m \le m'$ then $ n + m  \le n' + m'.$
   This is true, so this is a functor.

2. There is a functor $\otimes : (\N,\le) \times (\N,\le) \to (\N,\le)$ defined by sending $(a,b) \mapsto a \cdot b$ their multiplication.

   The program on arrows is this one: if $n \le n'$ and $m \le m'$ then $ n \cdot m  \le n' \cdot m'.$
   This is true, so this is a functor.

---

Also, note the similarity with products and coproducts: imagine that $\times$ and $+$ are the usual operations and that $X,Y,Z$ are numbers. Then, **let's write the number zero with 0 (which happens to be the initial object)** then:

- $0 \oplus X = X$.
- $X \oplus 0 = X$.
- $X \oplus (Y \oplus Z) = (X \oplus Y) \oplus Z$.

Which is very similar to the situation that happens with $+$ instead of $\oplus$!

But also, **let's write the number one with 1 (NOT the terminal object! There is no terminal object in this category if we do not assume that $\infty$ is a number)**. Then, look:

- $1 \otimes X = X$.
- $X \otimes 1 = X$.
- $X \otimes (Y \otimes Z) = (X \otimes Y) \otimes Z$.


# Example: the category of endofunctors $[C,C]$ and composition

Let's take the category $[C,C]$
Let me define a functor

$$
\,; : [C,C] \times [C,C] \to [C,C]
$$

- Choose ${;}$, to the functor sending $(F,G) \mapsto F\,; G$ to their composition.

Now, we need to check that ${;} : [C,C] \times [C,C] \to [C,C]$ really is a functor.
The program on objects is what we defined above.

For the program on arrows, we need to check that, given a pair of natural transformation
- $\alpha : F \to F'$ and
- $\beta : G \to G'$

 (i.e., an arrow in $[C,C] \times [C,C]$)

 there is an arrow in $[C,C]$ from $$F\,;G \longrightarrow F' \,;G'$$


 but this is exactly what we chose to construct with horizontal composition of natural transformations! You can check the rest of the structure to see that indeed it works.

 The special object in this category is the identity endofunctor $\textsf{id} : C \Rightarrow C$ of $[C,C]$:

- $\textsf{id} \,; X = X$.
- $X \,; \textsf{id} = X$.
- $X \,; (Y \,; Z) = (X \,; Y) \,; Z$.

- Choose $\otimes := {;}$, the functor sending $(F,G) \mapsto F\,; G$ to their composition.
- Choose the special object $I := \textsf{id}_C$,

And indeed, if you do $I \otimes F = \textsf{id}_C \,; F = F$ you get back the functor that you started with.

# Almost-example: any category with products

- Choose $\otimes := \times$ the functor sending $(A,B) \mapsto A \times B$ to their product,
- Choose $I := 1$ the terminal object.

We need to check that $\times : C \times C \to C$ really is a functor, done!

So, this works only if you create a new category, called the "strictification" of $C$, where you identify any two products with literally the same object.

# Definition: (strict) monoidal category

A *strict monoidal category* is a choice of the following things:
1. A category $C$,
1. A specific object $I$ of $C$ called "*monoidal unit*" or just "*unit*",
2. A functor $\otimes : C \times C \to C$ called "*tensor product*" or just "*tensor*" (I will try to call it "*combination functor*" or "*combine*"),

such that,

4. *(Strict object associativity)* For any objects $A,B,C$, the following two objects are literally *the same* object: $$(A \otimes B) \otimes C = A \otimes (B \otimes C)$$
5. *(Strict object unitality)* For any object $A$, $$I \otimes A = A, \\ A \otimes I = A,$$
6. *(Arrow associativity)* For any arrow $f : A \to A', g : B \to B', h : C \to C'$, these two arrows are the same: $$(f \otimes g) \otimes h = f \otimes (g \otimes h),$$

    (here we are using the program on arrows of $\otimes$.)

    (the first arrow $(f \otimes g) \otimes h$ goes from

    $(A \otimes B) \otimes C \to (A' \otimes B') \otimes C'$)

    (the second arrow $f \otimes (g \otimes h)$ goes from

    $A \otimes (B \otimes C) \to A' \otimes (B' \otimes C')$)

    But I can ask that these two are the same essentially because $(A \otimes B) \otimes C = A \otimes (B \otimes C)$ and $(A' \otimes B') \otimes C' = A' \otimes (B' \otimes C')$.

7. *(Arrow unitality)* For any arrow $f : A \to A'$ these two arrows are the same: $$\textsf{id}_I \otimes f = f, \\ f \otimes \textsf{id}_I = f,$$

This is called an *evil* notion (https://ncatlab.org/nlab/show/evil, ctrl+f "evil") because it involves equality of objects in points 3. and 4. (points 5. and 6. implicitly use these two points too.)

## Observation

  Given a normal category $C$, there might be more than one choice of unit $I$ and combine functor $\otimes$! We saw one example above, the preorder $(\N, \le)$ is the same but we can put two different monoidal operations "on top" of it.
