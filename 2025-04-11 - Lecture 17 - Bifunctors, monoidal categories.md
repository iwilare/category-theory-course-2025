# 2025-04-11 - Lecture 16 (Product of categories, monoidal categories)

Take the category $\textsf{Cat}$ where objects are categories and arrows are functors.

1. *Question.* Does this category have products?
2. *Question.* Does this category have terminal objects?
3. *Question.* Does this category have coproducts?
4. *Question.* Does this category have initial objects?
3. *Question.* Does this category have exponential objects?

# Definition: Product categories

So, someone comes up to me, and gives me two categories $C,D$.
Can I construct a third category, which I call $"C \times D"$ which satisfies the property of being a product of $C$ and $D$ in the category $\textsf{Cat}$?

1. *(Objects.)* The type of objects of this category is `Pair<A.Obj, B.Obj>`.
2. *(Arrows.)* I am given an object, which is a value of the type `P: Pair<A.Obj, B.Obj>`, and another value of the same type of objects, `Q: Pair<A.Obj, B.Obj>`.

I need to choose a new type `Arr_P_Q`.

Because `P,Q` are pairs, it makes sense to think of `P:=(a,b)` for some `a:C.Obj` and `b:D.Obj`, and similarly for `Q:=(a',b')` for some `a':C.Obj` and `b':D.Obj`.

I define the type of arrows going from `P:=(a,b)` to `Q:=(a',b')` to be what type? The type `Pair<C.Arr_a_a', D.Arr_b_b'>`.

So, in short:

$$\texttt{Arr\_(a,b)\_(a',b') := Pair<C.Arr\_a\_a', D.Arr\_b\_b'>}$$

3. *(Identities.)*
We need to give a value of this type:

$$\texttt{Arr\_(a,b)\_(a,b) = Pair<C.Arr\_a\_a, D.Arr\_b\_b>} $$

There is a value! It's the value `Pair(id[a], id[b])`.

4. *(Composition.)*
Imagine that someone comes up with two arrows: `(f,g): Arr_P_Q := Pair<C.Arr_a1_a2, D.Arr_b1_b2>` and `(f',g'): Pair<C.Arr_a2_a3, D.Arr_b2_b3>`.

Pictorially:

```
    a1      b1
     |      |
   f |      | g
     v      v
    a2      b2
     |      |
  f' |      | g'
     v      v
    a3      b3

    (a1,b1)              (a1,b1)
     | |                  | |
     | | (f,g)            | |
     v,v                  | |
    (a2,b2)               | | (f;f', g;g')
     | |                  | |
     | | (f',g')          | |
     v v                  v v
    (a3,b3)              (a3,b3)
```

5. *(Unitality left/right.)*
6. *(Associativity.)*

After doing this, you need to check that what you gave really is a product of $C$ and $D$.

1. *(Projections).* I need to construct two arrows of this category ($\textsf{Cat}$), like this:
    1. $\textsf{fst} : (C \times D) \to C$, so I need to construct a functor $\textsf{fst} : (C \times D) \Rightarrow C$.
        1. *(Program on objects.)* So, `fn fst_obj(a: CxD.Obj) -> C.Obj {...}` must be given.
        1. *(Program on arrows.)* So, `fn fst_arrows[A,B](a: ArrCxD_A_B) ArrC_(fst_obj(A))_(fst_obj(B)) -> C.Obj {...}` must be given.
    2. $\textsf{snd} : (C \times D) \to D$, so I need to construct a functor $\textsf{snd} : (C \times D) \Rightarrow D$.
2. *(Pairing arrow).* ...
3. ...

# Example: the $\times : C \times C \Rightarrow C$ functor

# Example: the $+ : C \times C \Rightarrow C$ functor

# Example: the ${\Rightarrow} : C^\textsf{op} \times C \Rightarrow C$ functor

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

We need to check that ${;} : [C,C] \times [C,C] \to [C,C]$ really is a functor.

# Almost-example: any category with products as a (non-strict!!) monoidal category

- Choose $\otimes := \times$ the functor sending $(A,B) \mapsto A \times B$ to their product,
- Choose $I := 1$ the terminal object.

We need to check that $\times : C \times C \to C$ really is a functor, done!



<!-- # Monoidal closed categories -->
