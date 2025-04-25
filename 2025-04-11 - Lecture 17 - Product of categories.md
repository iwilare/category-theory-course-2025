# 2025-04-11 - Lecture 16 (Product of categories, monoidal categories)

#### Meta-comment: this lecture is shorter because on this day we finished the proof of the Yoneda lemma.

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

*(Claim.)* $\textrm{Cat}$ has all products, given by the definition above.

## Definition: the terminal category $1$

```rust
struct Unit {
}
```

1. *(Object).* We pick as `Obj` The type `Unit`.
2. *(Arrows).* We pick as `Arr_Obj_Obj` the type `Unit`.
3. *(Identities).* For the identity on the object `unit()` (note! this is an object of the category. Do not mistake it for the type of objects `Unit`), we pick the value `unit(): Arr_unit_unit`.
4. *(Composition).* We can only make this choice here:
```
fn compose[unit()][unit()][unit()](a: Arr_unit_unit, b: Arr_unit_unit): Arr_unit_unit { ... }
fn compose[unit()][unit()][unit()](a: Unit, b: Unit): Unit {
    unit()
}
```

This choice works because the identity is just `unit()`.

#### What do we need to show now if we want to claim that category here is the terminal object in $\textrm{Cat}$?

> For every object $C$ in the category there is an arrow from $A$ to this terminal object; any other arrow is equal to this one.

First, we build the arrow from some category $C$. An arrow in $\textrm{Cat}$ is a functor, so right now we build a functor $! : C \Rightarrow 1$
- *(Program on objects)*
```rust
fn !_obj(a: C.Obj) -> 1.Obj { ... }
fn !_obj(a: C.Obj) -> Unit {
    unit()
}
```
- *(Program on arrows)* The program on arrows for two objects $a,b$ of $C$:
```rust
fn !_arrows[a][b](a: C.Arr[a][b]) -> 1.Arr[!_obj(a)][!_obj(b)] { ... }
fn !_arrows[a][b](a: C.Arr[a][b]) -> 1.Arr[unit()][unit()] { ... }
fn !_arrows[a][b](a: C.Arr[a][b]) -> Unit {
    unit()
}
```

What does it mean for functors to be equal? Their program on objects and their programs on arrows the same/are program equivalent. In this case, the choice we had to make above is literally the only possible one, so any other functor $F : C \Rightarrow 1$ into the terminal category $1$ must be equal to this one.

*(Claim.)* $\textrm{Cat}$ has a terminal object, given by the definition above.

## Definition: the initial category $0$

```rust
enum Empty {
}
```

1. *(Object).* We pick as `Obj` The type `Empty`.
2. *(Arrows).* For every two objects `A,B`, i.e., values in the type `Obj`, we need to pick a type of arrows. There are not values, so we don't need to pick anything!
3. *(Identities).* For every object `A`, we need to pick something. There are no objects! There is nothing to pick.
4. *(Composition).* For every three objects `A,B,C`, we need to pick a function. There are no objects! There is nothing to pick.
5. *(Identity left).* For every two objects `A,B`, we need to check a condition. There are no objects! There is nothing to check.
5. *(Identity right).* For every two objects `A,B`, we need to check a condition. There are no objects! There is nothing to check.
5. *(Associativity).* For every four objects `A,B,C,D`, we need to check a condition. There are no objects! There is nothing to check.

#### What do we need to show now if we want to claim that category here is the initial object in $\textrm{Cat}$?

> For every object $C$ in the category there is an arrow from $A$ to this initial object; any other arrow is equal to this one.

First, we build the arrow from some category $C$. An arrow in $\textrm{Cat}$ is a functor, so right now we build a functor $! : 0 \Rightarrow C$
- *(Program on objects)*
```rust
fn !_obj(a: 0.Obj) -> C.Obj { ... }
fn !_obj(a: Empty) -> C.Obj {
    match a {
        // nothing!
    }
}
```
- *(Program on arrows)* We need to define a program on arrows for every two possible objects $a,b$ of the first category, in this case $0$. But there are not objects! So we are done.

Again, in this case, the choice we had to make above is literally the only possible one, so any other functor $F : 0 \Rightarrow C$ into the initial category $0$ must be equal to this one.

*(Claim.)* $\textrm{Cat}$ has an initial object, given by the definition above.
