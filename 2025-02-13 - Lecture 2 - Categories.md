# 2025-02-13 - Lecture 2 (Categories)

## Today's plan

- Dissect a programming language into its components (composition as substitution)
- Give the definition of category
- Define new programming languages by defining new categories

At the end of the day, *what is a programming language?*
Category theory gives one possible *structured, formal, precise* answer to this question.

**For the rest of this lecture (and the next ones) we will take a restricted version of Rust: no infinite loops, no printing, no writing to files, no outside communication.**

Intuition, a *category is a programming language* with a set of types and a set of programs between each type.

This induces a graph built in the following way:

- Objects: the types of the programming language.
- Edges: an edge for each program that goes from one type to the other.

# Intuition for composition

Why composition? Composition corresponds to syntactic substitution of programs: this is achieved by replacing the input variable of the second program with the entire body of the first program:
```rust
fn program1(a: A) -> B {
  ...... a ....... a ....... a ...
}

fn program2(b: B) -> C {
  ... b ........ b ...
}

fn program3(c: C) -> D {
  ..... c ...... c ...
}
```

What does it mean to compose programs?
There are two possible ways in which one can answer this question.

- The first is the syntactic one: composing programs means substituting the code of one into the code of the other, i.e., I take the first program and replace the input variable of the second program with the code of the first whenever the input variable appears.
This is *syntactic substitution*. Syntactic composition is associative: this is why we will require an associativity law when combining programs together.

  ```rust
  /////////////////////////////////////
  // Composite program, syntactically:
  fn composition123_syn(a: A) -> D {
    ... program2(program1(a)) ...
    .. program2(program1(a)) ...
  }

  // First, take the code of program3, and put program2(program1(a))
  fn composition123_syn(a: A) -> D {
    ... (... program1(a) .......... program1(a) ...) ...
    .. (... program1(a) .......... program1(a) ...) ...
  }

  fn composition32(b: B) -> D {
    ... (... b .......... b ...) ...
    .. (... b .......... b ...) ...
  }
  fn composition32_then_1(a: A) -> D {
    ... (... program1(a) .......... program1(a) ...) ...
    .. (... program1(a) .......... program1(a) ...) ...
  }
  ```

- The other intuition for composition is the *semantic composition*: this is the kind of composition that happens
at runtime, i.e., feed the *data* produced by the execution of the first into the second.
This is typically much simpler to achieve: you just call the first function and feed its input into the second.
This is also associative.

  ```rust
  // Composite program, semantically:
  fn composition123(a: A) -> D {
    program3(program2(program1(a)))
  }

  fn composition23(b: B) -> D {
    program3(program2(b))
  }
  fn composition23_then_1(a: A) -> D {
    composition23(program1(a))
  }
  fn composition23_then_1(a: A) -> D {
    program3(program2(program1(a)))
  }
  ```


This kind of substitution/composition behaviour is typical of programs.
A good mathematical model for programming languages ought to satisfy exactly this subtitution behaviour.

## Intuition for identities

```rust
// The function which simply returns whatever input you have to it.
// Note that the type of the argument matches exactly the type of the output.
fn identity(i: i32) -> i32 {
  i
}
```

### Categories are a way to *symbolically* capture the situations above. We will have a symbol (e.g., a value in a Rust `enum`) for each type, and for each two symbols for types we will have a collection (again, some Rust `enum`) of symbols for programs.

# Categories

A category is defined by the following choices:

1. *(Objects.)* A choice of a *Rust type*, which we call `Obj`. Each value `a:Obj` in this type is going to be called "an object of the category". <br> **Intuition:** every value/symbol of this type is going to represent *a type of our programming language.*
2. *(Arrows.)* For every possible value `a: Obj` and `b: Obj`, a choice of another *Rust type*, which we call `Arr[a,b]`. Each value `f:Arr[a,b]` in one of these types is going to be called *"an arrow of the category, going from `a` to `b`"*. <br> **Intuition:** every value/symbol of this type is going to represent *an expression of our programming language which takes something of type `a` as input and gives me something of type `b` as output.
3. *(Identities.)* For every possible value `a: Obj`, a choice of a value `id[a]: Arr[a,a]` in the type `Arr[a,a]`.
**Intuition:** there always exists a symbol for the program which takes an input of type `a` and immediate returns that something of type `a`.
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

The key intuition is that in the first part of the equation I have to do an *intermediate step*: I have to construct an intermediate
program in which I substitute the code of the first program (f) into the code of the second program (g). The line at the top proceeds in the usual way.

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

## Terminology

You will often hear other books use the work "morphism" instead of arrow, and "hom-set" intead of "type of arrows" (for some objects $A$,$B$). Morphism can be a bit of a misleading word: not every arrow in a category represents a "transformation", or a process. We will later see during this lecture an example where arrows are just numbers.

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

fn compose_Int_Bool_Int(f: Arr_Int_Bool, g: Arr_Bool_Int): Arr_Int_Int {
  IntIdentity
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
fn compose_Bool_Int_Bool(f: Arr_Bool_Int, g: Arr_Int_Bool): Arr_Bool_Bool {
  match f { }
}

// Answer in https://rot13.com/: obgu pubvprf jbex orpnhfr jr zhfg purpx havgnyvgl/nffbpvngvivgl sbe rirel inyhr bs Nee_Obby_Vag, bs juvpu gurer ner abar! Fb gurer vf abguvat gb purpx.

```

In general, for the choices above (not for this last example), we now have to show that these choices satisfy the category laws!
1. I must check that whenever f is `BoolIdentity` the function `compose` returns g.
2. I must check that whenever f is `IntIdentity` the function `compose` returns g.
3. I must check that whenever g is `BoolIdentity` the function `compose` returns f.
4. I must check that whenever g is `IntIdentity` the function `compose` returns f.
5. Associativity.

# Example of category: a programming language with pairs

```rust

// Intuition for the type Pair<Int,Bool>
//   struct IntAndBool {
//     a: Int,
//     b: Bool,
//   }
// Intuition for the type Either<Int,Bool>
//   enum EitherIntBool {
//     Int(i32),
//     Bool(bool),
//   }
// Intuition for Either<A,B>
//   enum Either<A,B> {
//     left: A
//     right: B
//   }
// Intuition for Pair<A,B>
//   struct Pair<A,B> {
//     left: A
//     right: B
//   }

// Recursive algebraic data type
enum Obj {
  Int,
  Bool,
  Pair(Obj,Obj),
  Either(Obj,Obj),
}

// What are some examples of objects?
// - Int
// - Bool
// - Pair(Int, Int)
// - Pair(Int, Pair(Int, Int))
// - Pair(Int, Bool)
// - Either(Bool, Bool)

// We now define some examples of morphisms
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
/*
// Intuition for duplicate
fn duplicate(n: i32) -> Pair<Int,Int> {
  Pair { left: n, right: n }
}
*/
```

# Example of category: natural numbers and monoid structures

```rust
enum Obj {
  One
}

// Type synonym. Whenever you see Arr_One_One you can simply replace it with the type on the right.
// (In Rust this is typically used to make things more readable.)
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

The above situation can be abstracted with the following definition:

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

# Example of category: another important monoid structure

```rust
enum Obj {
  One // keep in mind that I can simply pick anything here
}

type Arr_One_One = str

// As identity, we pick the empty string.
let id = "";

fn compose_One_One_One(f: Arr_One_One, g: Arr_One_One) -> Arr_One_One {
  f + g // string concatenation
}
```

This works because concatenation of strings is associative, and because concatenating with the empty string just gives you the other string.
More in general, one can think of strings as *lists* of characters.

Assume we pick some type `A`, for instance `i32`. This will give us the type of lists `List<i32>`.

```rust
enum Obj {
  One // keep in mind that I can simply pick anything here
}

enum List<A> {
  Empty,
  Element { value: A, tail: List<A> }
}

type Arr_One_One = List<A>

// As identity, we pick the empty list.
let id = [];

// Solution for the exercise in Lecture 1
fn append<A>(a: List<A>, b: List<A>) -> List<A> {
    match a {
        Empty => b,
        Element { value: v, tail: t } =>
          Element { value: v, tail: append(t, b) }
    }
}

fn compose_One_One_One(f: Arr_One_One, g: Arr_One_One) -> Arr_One_One {
  append(f, g) // concatenation of lists
}
```

## Conclusion: a category with one object is the same thing as a monoid.

## Important takeaway: objects are not always """types in a programming language""" and arrows are not always """programs in a programming language""": they *can* be thought of such, but that need not be the case.
- In one of the examples for monoids, we saw that a program is a number!! Very strange. A number surely does not represent a process, nor a function, nor a transformation.
