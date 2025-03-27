# 2025-03-21 - Lecture 11 (Functors)

# Today's plan

## Functors as models

A *model* of a category $C$ is simply a functor $C \Rightarrow \text{Prog}$.

This idea allows us to take a category/programming language, which is purely symbolic, and *instantiate* each object/type with a concrete Rust type and each arrow/program with a concrete Rust program of a programming language.

What does this amount to?

If you take the category where the type of `Obj` is just `Int`, `Bool`, and `Float`, then a possible choice is this one:

```rust
// These are just symbols...
enum C.Obj {
    Int,
    Float,
    Bool,
}

enum C.Arr_Int_Bool {
    IsEven
}
```

But I can give it a real interpretation, via a functor $C \to \text{Prog}$ that assigns...

*Insert drawing here.*

```rust
fn F_obj(a: C.Obj) -> Prog.Obj {
    match a {
        Int => "i32",
        Float => "f32",
        Bool => "bool",
    }
}
```
...continuing...
```rust

// For each possible a,b:C.Obj, define a function like this:
//    fn F_arrow[a,b](a: C.Arr[a,b]) -> Prog.Arr[F_obj(a), F_obj(b)]

...

fn F_arrow[Int,Bool](Int: C.Arr[Int,Bool]) -> Prog.Arr[F_obj(Int), F_obj(Bool)] {
    match a {
        IsEven => "fn IsEven(a: Int) -> Bool { a % 2 == 0 }",
    }
}
```

In such a way that now, when I use my "fake" symbolic programming language, it's as if I was really doing this in $\textsf{Prog}$:

```rust
type Int = i32
type Bool = bool
type Float = f32

fn IsEven(a: Int) -> Bool {
    a % 2 == 0
}
```

# Functors from a preorder into another preorder (aka: monotone functions)

Take the preorder $(\N, \le)$ where objects are natural numbers and there is a single arrow $n \to m$ whenever $n \le m$.

$$\quad \quad \quad \quad \quad \quad \quad 0 \to 1 \to 2 \to \cdots$$

Take the preorder $(\texttt{int}, \le)$ where objects are integers and there is a single arrow $n \to m$ whenever $n \le m$.

$$\cdots -2 \to -1 \to 0 \to 1 \to 2 \to \cdots$$

#### We will create a functor $\textsf{tonat} : (\texttt{int}, \le) \Rightarrow (\N, \le)$:

1. *(Program on objects)* a choice of a Rust program `F_obj` with the following signature:
    ```rust
    fn tonat_obj(n: int) -> nat {
        if n >= 0 {
            n
        } else {
            0
        }
    }
    ```
2. *(Programs on arrows)* for every value `a:int`, `b:int` a choice of a Rust program `F_arrows[a][b]` that sends values in `C.Arr[a,b]` to values in `D.Arr[F_obj(a), F_obj(b)]`. Note! This is a preorder, so `C.Arr[a,b]` either has one element or it does not: we just need to show that if $a \le b$ as integers then $a \le b$ as natural numbers.

    There are four cases to consider:
    1. There is no value in `C.Arr[a,b]` for some integers $a,b$:
       then the function is defined from an empty type, so we can simply pattern match on the argument and we are done.
    2. There is a value in `C.Arr[a,b]`, so $a \le b$. We now need to provide a value inside  `D.Arr[F_obj(a), F_obj(b)]`, so we need to show that $\texttt{tonat\_obj}(a) \le \texttt{tonat\_obj}(b)$.

       From here there are two cases.
       1. *(Case $b < 0$.)* so that the condition is false, so we need to show that  $\texttt{tonat\_obj}(a) \le 0$; but since $a \le b < 0$ we also know that $a < 0$, so $\texttt{tonat\_obj}(a) = 0$. Therefore we just need to show that $0 \le 0$, which is true.
       2. *(Case $b \ge 0$.)* so that the condition is true.

          From here there are two cases.

          1. *(Case $a < 0$.)* Then $\texttt{tonat\_obj}(a) = 0 \le \texttt{tonat\_obj}(b)$, which is always true.
          2. *(Case $a \ge 0$.)* Both numbers are greater than zero, so we need to show that $a \le b$, which is exactly the assumption we have.

3. *(`F_arrows` respects identities)*: for every `a:C.Obj`, we know that there is a value `id[a]:C.Arr[a,a]`. Calling `F_arrows[a][a]` on that value must give you exactly the identity in the corresponding type `D.Arr[F_obj(a), F_obj(a)]`, i.e., this equation holds:
```rust
F_arrows[a][a](C.id[a]) = D.id[F_obj(a)]
```
*We don't need to check this! The category we end up into is a preorder, so every equation holds.*

4. *(`F_arrows` respects compositions, also called "functoriality")*: for every `a,b,c:C.Obj` and every `f:C.Arr[a,b]`, `g:Arr[b,c]`, this equation holds:
```rust
F_arrows[a][c](C.compose(f, g)) =
D.compose(F_arrows[a][b](f), F_arrows[b][c](g))
```
*We don't need to check this! The category we end up into is a preorder, so every equation holds.*

# Identity functor

For every category $C$, there is a stupid functor $\textsf{id}_C : C \Rightarrow C$

1. *(Program on objects)* we choose the Rust program:
    ```rust
    fn F_obj(a: C.Obj) -> C.Obj {
        a
    }
    ```
2. *(Programs on arrows)* for every value `a:C.Obj`, `b:C.Obj` a choice of a Rust program `F_arrows[a][b]`
    ```rust
    fn F_arrows[a][b](f: C.Arr[a,b]) -> D.Arr[F_obj(a), F_obj(b)] { ... }
    fn F_arrows[a][b](f: C.Arr[a,b]) -> C.Arr[a,b] {
        f
    }
    ```
3. *(`F_arrows` respects identities)*: for every `a:C.Obj`, we know that there is a value `id[a]:C.Arr[a,a]`. Calling `F_arrows[a][a]` on that value must give you exactly the identity in the corresponding type `D.Arr[F_obj(a), F_obj(a)]`, i.e., this equation holds:
    ```rust
    F_arrows[a][a](C.id[a]) = D.id[F_obj(a)]
    F_arrows[a][a](C.id[a]) = C.id[F_obj(a)]
                    C.id[a] = C.id[a]
    ```
4. *(`F_arrows` respects compositions, also called "functoriality")*: for every `a,b,c:C.Obj` and every `f:C.Arr[a,b]`, `g:Arr[b,c]`, this equation holds:
    ```rust
    F_arrows[a][c](C.compose(f, g)) = D.compose(F_arrows[a][b](f), F_arrows[b][c](g))
    F_arrows[a][c](C.compose(f, g)) = C.compose(f, g)
                   C.compose(f, g)  = C.compose(f, g)
    ```

# Composition of two functors

Given three categories $C,D,E$ and two functors $F : C \Rightarrow D, G: D \Rightarrow E$, I can cook up a new functor, which I call $F \,; G : C \Rightarrow E$, defined as follows:
1. *(Program on objects)* we choose the Rust program:
    ```rust
    fn FthenG_obj(a: C.Obj) -> E.Obj {
        G_obj(F_obj(a))
    }
    ```
2. *(Programs on arrows)* for every value `a:C.Obj`, `b:C.Obj` a choice of a Rust program `FthenG_arrows[a][b]`
    ```rust
    fn FthenG_arrows[a][b](f: C.Arr[a,b]) -> E.Arr[FthenG_obj(a), FthenG_obj(b)] { ... }
    fn FthenG_arrows[a][b](f: C.Arr[a,b]) -> E.Arr[G(F(a)), G(F(b))] { ... }
    fn FthenG_arrows[a][b](f: C.Arr[a,b]) -> E.Arr[G(F(a)), G(F(b))] {
        let k: D.Arr[F_obj(a),F_obj(b)] = F_arrows[a][b](f)
        let m: E.Arr[G_obj(F_obj(a)),G_obj(F_obj(b))] = G_arrows[F_obj(a)][F_obj(b)](k)
        let p: E.Arr[FthenG_obj(a),FthenG_obj(b)] = m
        p
        // alternatively,
        G_arrows[F_obj(a)][F_obj(b)](F_arrows[a][b](f))
    }
    ```
3. *(`FthenG_arrows` respects identities)*: for every `a:C.Obj`, we know that there is a value `id[a]:C.Arr[a,a]`. Calling `FthenG_arrows[a][a]` on that value must give you exactly the identity in the corresponding type `D.Arr[FthenG_obj(a), FthenG_obj(a)]`, i.e., this equation holds:
    ```rust
      FthenG_arrows(C.id[a])        // Unfold the definition of FthenG_arrows,
    = G_arrow(F_arrow(C.id[F(a)]))  // since F_arrow(C.id[a]) = D.id[a], since F also sends identities to identities,
    = G_arrow(D.id[F(a)])           // G also sends identities to identities.
    = E.id[G(F(a))]
    ```
4. *(`FthenG_arrows` respects compositions, also called "functoriality")*: for every `a,b,c:C.Obj` and every `f:C.Arr[a,b]`, `g:Arr[b,c]`, this equation holds:
    ```rust
    FthenG_arrows[a][c](C.compose(f, g)) = ...
    // (same game)
    ```

# Definition: the category of categories $\text{Cat}$.

Like $\text{Prog}$ this is one of those categories that is somewhat complicated and that we cannot define in Rust.

- *(Objects.)* The type of objects has a value for each possible category that you can think of (there is a lot of them!)
- *(Arrows.)* Given two categories $C,D$, the type of arrows going from $C$ to $D$ has a symbol for each possible functor that you can have from $C$ to $D$.
- *(Identities.)* Given a category $C$, there is a functor $\textsf{id}_C : C \Rightarrow C$, called the *identity functor*, which...
- *(Composition.)* Given categories $C,D,E$ and two functors $F : C \Rightarrow D$ and $G : D \Rightarrow E$, there is a functor $F\,;G : C \Rightarrow E$.
- *(Identity left.)* ...
- *(Identity right.)* ...
- *(Associativity.)* ...

# Endoarrow

In any category $C$, an arrow $f : A \to A$ for some object $A$ is called an endoarrow (because source and target of the arrow are the same).

# Endofunctors

A functor $F : C \Rightarrow C$ for some category $C$ is called an endofunctor (because source category and target category of the functor are the same).

# Examples of endofunctors

> An endofunctor is simply an endomorphism of $\text{Cat}$.

Endofunctors are much more common in computer science and programming languages: this the idea why:
1. When you work in a programming language, you only ever talk about the *same* programming language (of course, you just don't talk about two programming languages at the same time!).
2. As we saw, a category corresponds with a programming language since objects are types and arrows are programs.
3. In most programming languages, there is a way to get a new type whenever someone gives you another type.

### Example in $\text{Prog}$: `X` $\mapsto$ `List<X>`

1. *(Program on objects)*, exactly the thing above.
```rust
fn List_obj(X: Prog.Obj) -> Prog.Obj {
    "List<" + X + ">"
}
```

```rust
fn List_arrows[a][b](f: Prog.Arr[a][b]) -> Prog.Arr[List_obj[a][List_obj[b]] {
   "fn List_arrows_f(a: List<A>) -> List<B> {
        match a {
            EmptyList => EmptyList,
            Element(a, as) => Element(" + f + "(a), List_arrows_f(as))
        }
    }"
}
```

Effectively, what we need to show in that in Rust, whenever someone gives you a program

```rust
fn f(a: A) -> B { ... }
```


for some types `A` and `B`,
you need to give me a program with the following signature:

```rust
fn List_arrows_f(a: List<A>) -> List<B> {
    match a {
        EmptyList => EmptyList,
        Element(a, as) => Element(f(a), List_arrows_f(as))
    }
}
```


*This program here is related but it not what is required from the definition of functor.*

```rust
fn fmap(f: A -> B) -> Func<List<A>, List<B>> {
    ...
}
```



### Exercise: define functors in $\text{Prog}$: `X` is sent to `Maybe<X>`

### Exercise: define functors in $\text{Prog}$: `X` is sent to `Pair<X, X>`
### Exercise: define functors in $\text{Prog}$, for any `A` type: `X` is sent to `Pair<A, X>`

### Exercise: define functors in $\text{Prog}$, for any `A` type: `X` is sent to `Either<A, X>`

### Exercise: define functors in $\text{Prog}$, for any `A` type: `X` is sent to `Func<A, X>`
