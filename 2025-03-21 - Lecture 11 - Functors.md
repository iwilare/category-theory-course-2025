# 2025-03-21 - Lecture 11 (Functors)

# Today's plan

- Give the definition of functors.
- See examples.

# Functors

In short: functors are "arrows between entire categories".

Idea behind functors: functors embody the idea of interpreting the types and programs of a programming language into another category.

Given two categories, $C,D$, a functor $F : C \Rightarrow D$ amounts to the following choices (we use `C.asdf` and `D.asdf` to denote the choices of their respective categories):
1. *(Program on objects)* a choice of a Rust program `F_obj` with the following signature:
    ```rust
    fn F_obj(a: C.Obj) -> D.Obj {
        ...
    }
    ```
2. *(Programs on arrows)* for every value `a:C.Obj`, `b:C.Obj` a choice of a Rust program `F_arrows[a][b]`
    ```rust
    fn F_arrows[a][b](f: C.Arr[a,b]) -> D.Arr[F_obj(a), F_obj(b)] {
        ...
    }
    ```

3. *(`F_arrows` respects identities)*: for every `a:C.Obj`, we know that there is a value `id[a]:C.Arr[a,a]`. Calling `F_arrows[a][a]` on that value must give you exactly the identity in the corresponding type `D.Arr[F_obj(a), F_obj(a)]`, i.e., this equation holds:
    ```rust
    F_arrows[a][a](C.id[a]) = D.id[F_obj(a)]
    ```
    $$F(\textsf{id}_A) = \textsf{id}_{F(A)}$$

4. *(`F_arrows` respects compositions, also called "functoriality")*: for every `a,b,c:C.Obj` and every `f:C.Arr[a,b]`, `g:Arr[b,c]`, this equation holds:
    ```rust
    F_arrows[a][c](C.compose(f, g)) =
    D.compose(F_arrows[a][b](f), F_arrows[b][c](g))
    ```
    $$F(f\,;g) = F(f)\,;F(g)$$

## Notation

Again, we can often omit the indices if we know the types of the arrows involved.

| Math notation | Rust notation |
|---|---|
| $F(A)$ | `F_obj(A)` |
| $F(f)$ | `F_arrows(f)` |

## Graphical intuition

The intuition is that functors "cast a shadow" from one category into the other.

**Drawing here.**

## Example

Take the following category:

```rust
// REMEMBER! THESE ARE NOT REAL TYPES, these are just symbols for a skeleton of programming language, or, in other words, its internal representation
enum C.Obj {
    Int,
    Float,
    String,
}

enum D.Obj {
    Float,
    ArrayChar,
    Bool,
}

enum C.Arr_Int_Int {
    Add3,
    Identity,
    ...
}

enum C.Arr_Int_Float {
    ConvertToFloat,
    AddThreeConvert,
    AddSixConvert,
    ...
}

enum C.Arr_Int_String {
    PrintIntToString,
}

///////////////

enum D.Arr_Float_String {
    FloatToString
}

fn F_obj(a: C.Obj) -> D.Obj {
    match a {
        Int    => Float,
        Float  => Float,
        String => String
    }
}

// For each possible a,b:C.Obj, define a function like this:
//    fn F_arrow[a,b](a: C.Arr[a,b]) -> Prog.Arr[F_obj(a), F_obj(b)]

// this is the case where a=Int, b=String:

fn F_arrows[Int,String](a: C.Arr[Int,String]) -> D.Arr[F_obj(Int),F_obj(String)] { ... }
fn F_arrows[Int,String](a: C.Arr[Int,String]) -> D.Arr[Float,String] {
    match a {
        PrintIntToString => FloatToString,
        ...
    }
}

enum D.Obj {
    Float,
    Bool,
}
```


## Functors as interpretations of languages

- Take $\textsf{Prog}$ the category of Rust types and Rust programs.
- Take $\textsf{Java}$ the category of Java types and Java programs.

A functor from $\textsf{Prog} \Rightarrow \textsf{Java}$ is essentially a way of translating Rust types into Java types and Rust programs into Java programs (using the translation on types).

Of course there are many possible ways of translating types! e.g.,

| Rust type | Java type |
|-|-|
| `i32` | `Integer` |
| `i64` | `Integer` |
| `Vec<i32>` | `ArrayList<Integer>` |
| `bool` | `boolean` (or...) ||
| `bool` | `Integer` |

## Constant functor on an object $A$

Given two categories $C,D$ and a chosen object `A:D.Obj`, there is a functor $const(A) : C \Rightarrow D$ that sends every object $X$ of $C$ to $A$.

```rust
enum D.Obj {
    ...
    A,
    ...
}

fn const(A)_obj(a: C.Obj) -> D.Obj {
    A
}

fn const(A)_arrows[a][b](f: C.Arr[a,b]) -> D.Obj[const(A)_Obj(a), const(A)_Obj(b)] { ... }
fn const(A)_arrows[a][b](f: C.Arr[a,b]) -> D.Obj[A, A] {
    id[A]
}
```

To check functoriality, I have to check that for every $f : C \to D$, $g : D \to E$,

$$
F(f \,; g) = F(f) \,; F(g)\\
\textsf{id}_A = \textsf{id}_A \,; \textsf{id}_A
$$
