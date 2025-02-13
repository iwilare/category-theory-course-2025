# 2025-02-13 - Lecture 2 (Categories)

## Today's plan

- Dissect a programming language into its components
- Give the definition of category
- Define new programming languages by defining new categories

At the end of the day, *what is a programming language?*
Category theory gives one possible *structured, formal, precise* answer to this question.

**For the rest of this lecture (and the next ones) we will take a restricted version of Rust: no infinite loops, no printing, no writing to files, no outside communication.**

## Dissection of a program as an equivalent composition of terms

# Graphical intuition for categories

Intuitively, a category is a programming language with a set of types and a set of programs between each type, such that:
- variables exist (i.e., programs that do nothing),
  ```rust
  fn identity(a: i32) -> i32 {
    a
  }
  ```


# Categories

A category is defined by the following choices:

1. *(Objects.)* A choice of a *Rust type*, which we call `Obj`. <br> **Intuition:** every value of this type is going to represent *a type of our programming language.*
2. *(Arrows.)* For every possible value `a: Obj` and `b: Obj`, a choice of another *Rust type*, which we call `Arr[a,b]`. <br> **Intuition:** every value of this type is going to represent *an expression of our programming language which takes something of type `a` as input and gives me something of type `b` as output.
3. *(Identities.)* For every possible value `a: Obj`, a choice of a value `id[a]: Arr[a,a]` in the type `Arr[a,a]`. **Intuition:** there always exists a program which takes an input of type `a` and immediate returns that something of type `a`.
4. *(Composition.)* For every possible value `a: Obj`, `b: Obj`, `c: Obj`, a choice of a Rust function with the following signature:
```rust
fn compose[a][b][c](f: Arr[a,b], g: Arr[b,c]) -> Arr[a,c] {
  ...
}
```
**Intuition:** for every two programs such that the output type of the first coincides with the output type of the second, there is always a "composite program" which combines the two processes together into a single process that, intuitively, does one after the other.

This concludes the part for choices/data. Now we ask that the choices above are in some sense well-behaved: the following three conditions are called the *category laws*:

5. *(Left unitality.)* Given a value `f: Arr[a,b]` for some values `a:Obj,b:Obj`, the following thing must happen: if you call the function `compose[a][a][b](id[a], f)` on `id[a]` as the first argument it must happen that this function returns exactly `f`. In other words,
```rust
compose[a][a][b](id[a], f) = f
```
6. *(Right unitality.)* Given a value `f: Arr[a,b]` for some values `a:Obj,b:Obj`, the following thing must happen: if you call the function `compose[a][b][b](f, id[b])` on `id[b]` as the second argument it must happen that this function returns exactly `f`. In other words,
```rust
compose[a][b][b](f, id[b]) = f
```
7. *(Associativity.)* For every value `a,b,c,d: Obj`, and every value `f:Arr[a,b], g:Arr[b,c], h:Arr[c,d]`, the following function calls must give you the same result:

```rust
  compose[a][b][d](f, compose[b][c][d](g, h))
= compose[a][c][d](compose[a][b][c](f, g), h)
```

*-- End of the definition of Category. --*
<hr>

## Notation

In the Rust notation, note that one can often remove the square brackets, because it's unambiguous what kind of element of `Obj` must be placed there for the entire expression to make sense: for example, if I know that `f:Arr[a,b]` and `g: Arr[b,c]` the indices of `compose(f, g)` must be `[a][b][c]`.

| Math notation | Rust notation |
|-|-|
| $1_a \quad \text{or} \quad \textsf{id}_a$ | `id[a]` |
| $f : a \to b$ | `f: Arr[a,b]` |
| $f \,; g \quad \text{or} \quad g \circ f$ | `compose(f, g)` |
| $f \,; \textsf{id}_b = f = \textsf{id}_a \,; f$ | (Identity laws) |
| $f \,; (g \,; h) = (f \,; g) \,; h$ | (Associativity) |

The associativity law is extremely important! It means that any way I put the brackets it does matter, so one can simply write $f \,; g \,; h$ and there cannot be any confusion.

# Example of category: a programming language with Int, Bool

In order to define a new category, I must make all the choices above; after that, I have to make sure that my choices satisfy the category laws.

For the type of objects `Obj`, I choose the following type:
```rust
enum Obj {
  Int,
  Bool
}
```
I make the following choices for arrows:

```rust
enum Arr_Int_Int {
  IntIdentity,
}
enum Arr_Int_Bool {
  IsEven,
  IsEvenNot,
}
enum Arr_Bool_Int {

}
enum Arr_Bool_Bool {
  BoolIdentity,
  Not
}
```

All possible choices that I have to make for `compose`:
```rust
fn compose_Int_Int_Int(f: Arr_Int_Int, g: Arr_Int_Int): Arr_Int_Int { ... }
fn compose_Int_Int_Bool(f: Arr_Int_Int, g: Arr_Int_Bool): Arr_Int_Bool { ... }
fn compose_Int_Bool_Int(f: Arr_Int_Bool, g: Arr_Bool_Int): Arr_Int_Int { ... }
fn compose_Int_Bool_Bool(f: Arr_Int_Bool, g: Arr_Bool_Bool): Arr_Int_Bool { ... }
fn compose_Bool_Int_Int(f: Arr_Bool_Int, g: Arr_Int_Int): Arr_Bool_Int { ... }
fn compose_Bool_Int_Bool(f: Arr_Bool_Int, g: Arr_Int_Bool): Arr_Bool_Bool { ... }
fn compose_Bool_Bool_Int(f: Arr_Bool_Bool, g: Arr_Bool_Int): Arr_Bool_Int { ... }
fn compose_Bool_Bool_Bool(f: Arr_Bool_Bool, g: Arr_Bool_Bool): Arr_Bool_Bool { ... }
```

Here are some cases:

```rust
fn compose_Int_Int_Int(f: Arr_Int_Int, g: Arr_Int_Int): Arr_Int_Int {
  match f {
    IntIdentity =>
      match g {
        IntIdentity => IntIdentity
      }
  }
}

fn compose_Bool_Bool_Bool(f: Arr_Bool_Bool, g: Arr_Bool_Bool): Arr_Bool_Bool {
  match f {
    BoolIdentity => g,
    Not =>
      match g {
        Not => BoolIdentity,
        BoolIdentity => Not,
      }
  }
}

fn compose_Int_Bool_Bool(f: Arr_Int_Bool, g: Arr_Bool_Bool): Arr_Int_Bool {
  match f {
    IsEven =>
      match g {
        BoolIdentity => IsEven,
        Not => IsEvenNot,
      },
    IsEvenNot =>
      match g {
        BoolIdentity => IsEvenNot
        Not => IsEven,
      }
  }
}

// There is nothing I have to specify since Arr_Bool_Int is the empty type!
// Other empty cases follow.

fn compose_Int_Bool_Int(f: Arr_Int_Bool, g: Arr_Bool_Int): Arr_Int_Int {
  match f {
    IsEven =>
      match g {

      }
    IsEvenNot =>
      match g {

      }
  }
}

fn compose_Bool_Int_Int(f: Arr_Bool_Int, g: Arr_Int_Int): Arr_Bool_Int {
  match f {

  }
}

fn compose_Int_Bool_Int(f: Arr_Int_Bool, g: Arr_Bool_Int): Arr_Int_Int {
  match g {

  }
}

// Which one of these two definitions works?
fn compose_Bool_Int_Bool(f: Arr_Bool_Int, g: Arr_Int_Bool): Arr_Bool_Bool {
  Not
}
fn compose_Bool_Int_Bool(f: Arr_Bool_Int, g: Arr_Int_Bool): Arr_Bool_Bool {
  BoolIdentity
}

// Answer in https://rot13.com/: obgu pubvprf jbex orpnhfr jr zhfg purpx havgnyvgl/nffbpvngvivgl sbe rirel inyhr bs Nee_Obby_Vag, bs juvpu gurer ner abar! Fb gurer vf abguvat gb purpx.

fn compose_Bool_Int_Bool(f: Arr_Bool_Int, g: Arr_Int_Bool): Arr_Bool_Bool {
  match f { }
}
```

Now we have to show that these choices satisfy the category laws!
1. I must check that whenever f is `BoolIdentity` the function `compose` returns g.
2. I must check that whenever f is `IntIdentity` the function `compose` returns g.
3. I must check that whenever g is `BoolIdentity` the function `compose` returns f.
4. I must check that whenever g is `IntIdentity` the function `compose` returns f.
5. Associativity.

# Example of category: a programming language with pairs

```rust
// Recursive algebraic data type
enum Obj {
  Int,
  Bool,
  Pair<Obj,Obj>
}

enum Arr_Int_Bool {
  IsEven,
  IsEvenNot,
}
enum Arr_PairIntInt_Int {
  First,
  Second,
  ...
}
enum Arr_Int_PairIntInt {
  Duplicate,
  ...
}
```

# Example of category: natural numbers and monoid structures

```rust
enum Obj {
  One
}

type Arr_One_One = i32
```

We have to pick a value `id[One] : Arr_One_One`.
At this point we have two possible choices:

### Choice A
1. Pick the value `0: i32`.
2. For the composition function, we pick this function:
   ```rust
    fn compose_One_One_One(f: Arr_One_One, g: Arr_One_One) -> Arr_One_One {
      f + g
    }
   ```
3. We have to check unitality: this boils down to the fact that for any value `g: Arr_One_One` the equations
   ```rust
   compose_One_One_One(0, f) = f   // Given f: i32,  0 + f = f
   compose_One_One_One(f, 0) = f   // Given f: i32,  f + 0 = f
   ```
   are satisfied. This is clearly true because of a general property of 0 and + for i32!
4. We have to check associativity: for every value `f,g,h:Arr_One_One`
   ``` rust
   compose_One_One_One(f, compose_One_One_One(g, h))
   compose_One_One_One(compose_One_One_One(f, g), h)
   ```
   this holds because of a general property of numbers: given `f,g,h:i32`, addition is associative: $f+(g+h) = (f+g)+h$.

   (This is the only index we have to check! We use it 4 times from the definition of associativity)

### Choice B
1. Pick the value `1: i32`.
2. For the composition function, we pick this function:
    ```rust
    fn compose_One_One_One(f: Arr_One_One, g: Arr_One_One) -> Arr_One_One {
      f * g
    }
    ```
3. Similar properties as above: $1 * f = f = f * 1$ and $(f * g) * h = f * (g * h)$.

## Monoid

We abstract the above situation.

1. *(Objects.)* A choice of a Rust type `M`.
2. *(Identity.)* A choice of a specific value `id:M` in the type `M`.
3. *(Operation.)* A choice of a Rust function with the following signature:
    ```rust
    fn op(a: M, b: M) -> M { ... }
    ```

Subject to the following laws:

4. *(Left unitality.)* For every value `a:M`, the following equation must hold:
   ```rust
   op(id, a) = a
   ```
5. *(Right unitality.)* For every value `a:M`, the following equation must hold:
   ```rust
   op(a, id) = a
   ```
6. *(Associativity.)* For every value `a,b,c:M`, the following equation must hold:
   ```rust
   op(a, op(b, c)) = op(op(a, b), c)
   ```

## Conclusion: a category with one object is the same thing as a monoid.

# Other categories

Unfortunately, Rust is not expressive enough for us to capture a lot of examples which are standard in math, but very clunky to formalize in Rust.
Some examples:

- The category where objects are monoids and morphisms are *monoid homomorphisms*
- The category where objects are groupds and morphisms are *group homomorphisms*
- The category where objects are topological spaces and morphisms are *continuous functions*

# The language of categories

## Algebraic language for categories
## Graphical language for categories - Pasting diagrams
## Graphical language for categories - String diagrams
## Term language for categories

Composition corresponds to syntactic substitution of programs: this is achieved by replacing the input variable of the second program with the entire body of the first program:
```rust
fn program2(b: B) -> C {
  ... b ...
  ........ b ...
}

// replace each occurrence of b with the entirety of the first program:

fn composite(a: A) -> C {
  ... program_1(a) ...
  ........ program_1(a) ...
}
```
