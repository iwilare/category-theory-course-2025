# 2025-04-11 - Lecture 16 (Product of categories, monoidal categories)

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

given by a family of arrows

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
& = & \langle \textsf{fst} \,; F(f), \textsf{fst} \,; G(f) \rangle \,; \textsf{fst} & \text{(product equation)} \\
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

# Definition: (strict) monoidal category

A category $C$ is called *strict monoidal* if it comes equipped with
1. A specific object $I$ of $C$ called "*monoidal unit*" or just "*unit*",
2. A functor $\otimes : C \times C \to C$ called "*tensor product*" or just "*tensor*" (I will try to call it "*combination functor*" or "*combine*"),

such that,

3. *(Strict object associativity)* For any objects $A,B,C$, the following two objects are literally *the same* object: $$(A \otimes B) \otimes C = (A \otimes B) \otimes C$$
4. *(Strict object unitality)* For any object $A$, $$I \otimes A = A, \\ A \otimes I = A,$$
5. *(Arrow associativity)* For any arrow $f : A \to A', g : B \to B', h : C \to C'$, these two arrows are the same: $$(f \otimes g) \otimes h = f \otimes (g \otimes h),$$

    (here we are using the program on arrows of $\otimes$.)

    (these two arrows go from $A \times (B \times C) \longrightarrow A' \times (B' \times C') = (A \times B) \times C$).
5. *(Arrow unitality)* For any arrow $f : A \to A'$ these two arrows are the same: $$\textsf{id}_I \otimes f = f, \\ f \otimes \textsf{id}_I = f,$$

This is called an *evil* notion (https://ncatlab.org/nlab/show/evil, ctrl+f "evil") because it involves equality of objects in points 3. and 4. (points 5. and 6. implicitly use these two points too.)

# Example: the preorder $(\N,\le)$ with monoidal structure $(0,+)$

- Choose $\otimes := +$, the functor sending $(a,b) \mapsto a+b$ to their sum,
- Choose $I := 0$,

We have to check that $+ : (\N,\le) \times (\N,\le) \to (\N,\le)$ really is a functor,

- If I know that $$(n,m) \le (n',m'),$$ which by definition of product category means that$$n \le m \text{ and } n' \le m',$$ then I need to show that $$F(n,m) \le F(n',m'),$$ which by definition of our functor $F$ in this is case means that $$n + m \le n' + m'$$ which is true because of a general property of natural numbers which we take as intuitive.

- Then, clearly the equations for strict monoidal categories above are true.
- Note! This is a preorder, we do not need to check any equation on the arrows, since in a preorder everything holds.

# Example: the preorder $(\N,\le)$ with monoidal structure $(1,*)$

- Choose $\otimes := *$, the functor sending $(a,b) \mapsto a+b$ to their multiplication,
- Choose $I := 1$,

We have to check that $* : (\N,\le) \times (\N,\le) \to (\N,\le)$ really is a functor,

- If I know that $$n \le m \text{ and } n' \le m',$$ then I need to show that $$n * m \le n' * m'$$ which is true because of a general property of natural numbers which we take as intuitive.

- Then, clearly the equations for strict monoidal categories above are true.

# Example: any category of endofunctors $[C,C]$ as a strict monoidal category

- Choose $\otimes := {;}$, the functor sending $(F,G) \mapsto F\,; G$ to their composition.
- Choose $I := \textsf{id}_C$,

And indeed, if you do $I \otimes F = \textsf{id}_C \,; F = F$ you get back the functor that you started with.

Now, we need to check that ${;} : [C,C] \times [C,C] \to [C,C]$ really is a functor.
The program on objects is what the defined above. For the program on arrows, we need to check that, given a pair of natural transformation $\alpha : F \to F'$ and $\beta : G \to G'$ (i.e., an arrow in $[C,C] \times [C,C]$) there is an arrow in $[C,C]$ from $F\,;F' \to G \,;G'$: but this is exactly what we chose to construct with horizontal composition of natural transformations! You can check the rest of the structure to see that indeed it works.

# Almost-example: any category with products as a (non-strict!!) monoidal category

- Choose $\otimes := \times$ the functor sending $(A,B) \mapsto A \times B$ to their product,
- Choose $I := 1$ the terminal object.

We need to check that $\times : C \times C \to C$ really is a functor, done!

So, this works only if you create a new category, called the "strictification" of $C$, where you identify any two products with literally the same object.

# A monoid in a monoidal category

<!-- # Monoidal closed categories -->
