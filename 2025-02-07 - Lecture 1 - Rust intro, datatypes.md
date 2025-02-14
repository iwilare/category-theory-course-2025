# 2025-02-07 - Lecture 1 (Rust intro, datatypes)

## Today's plan

- Introduce the Rust programming language
- Give an overview of Rust's syntax and features

Rust will play two important roles:
1. It will be the language we refer to when introducing examples of categories,
2. It will serve as the background which we use when defining other categories.

Mathematicians typically use *another background* and use other abstract examples.

Whenever you see "Minor" we will talk about a small feature that we do not particularly care about for our category theory purposes but that is important to know for real world Rust.

## Expressions vs. statements

One of the first things that confuse people when encountering Rust for the first time is the difference between expressions and statements, which in Rust (and, coincidentally, programming language theory) is particularly important.

In standard programming languages, there is a difference between expressions and statements: expressions *are*, and *evaluate to stuff*, whereas statements *do*.

```java
Integer v = a + 3;
b = b + f(4);
if b % 2 == 0 {
    start_query(name, password);
}
printf("Hello!", compute_data(b, extract_info(data), "192.168.0.1:8080"));
```

Expressions in this piece of code:
- `a + 3`
- `a`
- `f(4)`
- `b`
- `extract_info(data)`
- `compute_data(b, extract_info(data), "192.168.0.1:8080")`

We will particularly focus on expressions rather than statements in Rust, because we do not want to talk about effects (yet! We will do it when talking about monads.)

## The Rust programming language

```rust
// We write comments like this.

// This defines a new function called add_three, with
// one argument called x which is an integer, returning an integer.
// Note that no return keyword is needed.
fn add_three(x: i32) -> i32 {
    x + 3
}
fn is_even(a: i32) -> bool {
    a % 2 == 0
}
fn is_valid_grade(n: i32) -> bool {
    n <= 5
}

// New variables are defined using let.
// We call functions in the usual way.
// let is a statement, so it ends with a semicolon.
fn example_let(a: i32) -> i32 {
    let n: i32 = add_three(a);
    2 * n
}
// Variables in Rust are immutable!
// This example does NOT compile.
fn example(a: i32) -> i32 {
    // You can omit the type when it can be inferred from the context.
    let n = add_three(a);
    n = n + 1; // Error: trying to change `n` (you can declare `n` as `mut`)
    2 * n
}
fn example_mut(a: i32) -> i32 {
    let mut n = add_three(a);
    n = n + 1;
    2 * n
}
fn example_let_let(a: i32) -> i32 {
    let n = add_three(a);
    let n = n + 1; // This is okay! The previous `n` cannot be accessed anymore.
    2 * n
}

// Rust has print statements.
// Rust has string literals, and other kinds of strings.
fn print_some_stuff() {
    println!("This text");
    println!("Will take");
    let count = 3;
    println!("{} lines.", count);
}

// Rust has if-then-else statements.
fn odd_or_even(a: i32) -> i32 {
    if a % 2 == 0 {
        println!("Sorry to say this, but...");
        println!("the number supplied was even.");
    } else {
        println!("Great, it's odd! Here it is: {}", a);
    }
    a + 127
}

// When the last thing a function does is a statement, there is
// """no return type""", and we simply omit the ->.
fn example_no_return(a: i32) {
    if a % 2 == 0 {
        println!("The number supplied was even.");
    } else {
        println!("The number was odd! Here it is: {}", a);
    }
}

// In Rust, if-then-else statements evaluate.
fn bool_to_int(a: i32) {
    let c: &str = if a % 2 { "Even" } else { "Odd" };
    println!("Result {}", c);
}
fn is_even(a: bool) -> i32 {
    if a % 2 == 0 { 1 } else { 0 }
}

// Rust has methods, which are basically functions.
fn twice_length(s: &str) -> usize {
    2 * s.len() // is basically the same as 2 * len(s)
}

// Functions can take multiple arguments.
fn test(n: i32, a: bool) -> bool {
    is_valid_grade(n) && !a
}
```

## Struct types

It is often necessary to package data together, and pass it around "in a single package".
In Rust you can do this using `struct` types.
These are very similar to classes in Java.

```rust
struct Employee {
    name: String,
    age: i32,
    salary: f32,
}

// How do we create elements of this type? Using constructors:
// How do we access the data? We use the notation s.fieldname:
fn main() {
    // The constructor has the same name of the type.
    let gerry: Employee = Employee { name: "Gerry", age: 23, salary: 3.23 };
    let mike:  Employee = Employee { name: "Michael", age: 21, salary: 4.21 };

    println!("The employees: {}, {}", gerry, mike);

    let a: i32 = gerry.age;

    println!("Gerry's age: {}", a);
    println!("Mike's salary: {}", mike.salary);
}
```

Minor note: Rust supports methods calls.

```rust
impl Employee {
    fn get_salary(self) {
        self.salary
    }
    fn full_name(self, role: String) -> String {
        format!("Employee {}, role {}", self.name, role)
    }
    fn fire(self) {
        start_query("...");
    }
}
```

Here is another particularly degenerate example of `struct`, which has no fields!
This is an important example that we will come back later in the course.

```rust
struct NoFields {

}

fn main() {
    // We use the constructor NoFields by providing data for each field...
    // ...of which there are none.
    let a: NoFields = NoFields {}
    let b: NoFields = NoFields {}

    println!("Here are two examples with that type: {} {}", a, b);
}
```

## Enum types

```rust
// Create a new type with three possible elements.
// Red, Green and Yellow are called the constructors and always start with capital.
enum Semaphore {
    Red,
    Green,
    Yellow
}
// Question: "How do I create something of a type which is an enum?"
// Answer: you use one of the constructors Red, Green, Yellow.

// *Match expressions* allow you to do "case analysis" on the way that the data was created.
// These are *expressions*, they evaluate.
fn color_height(a: Semaphore) -> i32 {
    match a {
        Red => 100,
        Yellow => 50,
        Green => 10,
    }
}
fn main() {
    // We construct something of type Semaphore
    let a: i32 = color_height(Red);
    let b: i32 = color_height(Yellow);

    println("{} {}", a, b);
}
/*
You must always cover all branches! In Rust, matches are said to be exhaustive.
This is good for programming, since it ensures that you program will never crash because of a branching statement.
(e.g., in Haskell this is not the case)
*/
// These are expressions, so they can be used whenever an expression can be used.
fn color_nickname(a: Semaphore) {
    let prompt = match a {
        Semaphore::Red => "r",
        Semaphore::Yellow => "y",
        Semaphore::Green => "g" };
    println!("The color was: {}", prompt);
}

// Match statements can also contain statements (this function does not return anything)
fn can_you_go(a: Semaphore) {
    match a {
        Semaphore::Red => {
            println!("Stop!!");
        },
        Semaphore::Green => {
            println!("Go ahead");
        },
        Semaphore::Yellow => {
            println!("Caution");
        }
    }
}
// So far, we saw that a match statement has as many cases as the
// number of constructors of the type.
// This will always be the case for the category theory we care about.
// Minor remark:
//     Rust allows us, for our convenience, to be more expressive.
//     On the left of match cases you have *patterns*:
//     1. A pattern is either a constructor, like Semaphore::Red,
//        which succeeds only if the expression being matched corresponds with it,
//     2. Or a variable `a` which gets assigned to whatever the value is,
//        which succeeds always.
//     3. You can just put `_` instead of a variable for convenience.

// Catch all expressions
fn is_red(a: Semaphore) -> bool {
    match a {
        Red => true
        _ => false
    }
}
fn is_red(a: Semaphore) -> bool {
    match do_something_complicated(a) {
        Red => true
        b => {
            println!("The color supplied was: {}", b);
            b == Blue
        }
    }
}

// Enums allow you to capture information in a way that is independent of its representation.
// It would be much better to have a program return something like this rather than a string with the error message.
#[derive(Debug)]
enum HttpError {
    BadRequest,
    Unauthorized,
    Forbidden,
    NotFound,
    InternalServerError,
}

fn code(e: HttpError) -> u16 {
    match e {
        HttpError::BadRequest => 400,
        HttpError::Unauthorized => 401,
        HttpError::Forbidden => 403,
        HttpError::NotFound => 404,
        HttpError::InternalServerError => 500,
    }
}

fn to_string(e: HttpError) -> String {
    match e {
        HttpError::BadRequest => "400 Bad Request",
        HttpError::Unauthorized => "401 Unauthorized",
        HttpError::Forbidden => "403 Forbidden",
        HttpError::NotFound => "404 Not Found",
        HttpError::InternalServerError => "500 Internal Server Error",
    }
}

// Using enums you can also define booleans:
/*
enum bool {
    true,
    false
}
*/
enum MyBool {
    True,
    False,
}
// If-then-else is just an instance of match:
fn login(username: String, is_admin: MyBool) {
    match is_admin {
        MyBool::True => {
            println("Administrator login...");
        }
        MyBool::False => {
            println("User login...");
        }
    }
}
/*
> login("Andrea", MyBool::True)
User login...
> login("Andrea", MyBool::False)
Administrator login...
*/
```

## Algebraic data types

Is there a way to "combine" enums with structs?
Yes! The name given to this language construct is "ADT", or
algebraic data types. Why algebraic? We will learn it later during the course.

```rust
/*
Enum and struct types can be combined together.
In particular, Enums can contain data in each of their cases.
The intuition is that an Employee is *either* constructed
using the Student constructor, or using the Teacher constructor.

Each constructor needs to be called with some data, which can then be
extracted using match.
*/
// This creates the type Employee, with two constructors Student and Teacher.
enum Employee {
    Student { surname: String, avg: f32 },
    Teacher { name: String }
}

// How do we create elements of this type?
// How do we use these elements?
fn main() {
    let satoshi: Employee = Student { surname: "Nakamoto", avg: 4.53 }
    let mark: Employee = Student { surname: "Uustalu", avg: 3.21 }
    let me: Employee = Teacher { name: "Andrea" }
    let fosco: Employee = Teacher { name: "Fosco" }

    println!("People in the class: {} {} {}", satoshi, mark, me, fosco);
}
fn get_teacher_name(e: Employee) -> String {
    match e {
        Employee::Student { surname: s, avg: a } =>
            // In this branch I can use the surname s: String and the average a: f32.
            format!("Error! The employee provided is a student! Their surname is {}", s),
        Employee::Teacher { name: n } =>
            // In this branch I can use the name n: String.
            n
    }
}
// Minor point: If you're a true programmer you're lazy! You can instead write:
//    1. You can write
//              Employee::Student { surname, avg }
//       instead of
//              Employee::Student { surname: surname, avg: avg }
//    2. You can use `_` to avoid having to think of names to fields that you do not need.

// Examples:
> get_teacher_name(Teacher { name: "Andrea" })
"Andrea"
> get_teacher_name(Student { surname: "Lox", avg: 4.27 })
"Error! The employee provided is a student! Their surname is Lox"

/////////////////////////////////////////////////////////////////////////////////////////////////

// Another small example: capturing the state of an application
enum AppState {
    Login {
        textbox: String,
        passbox: String
    },
    Main {
        user_id: i32,
        counter_state: i32,
        is_button_pressed: bool,
    },
    Scene2 {
        user_id: i32,
        input_text: String,
    }
}

/////////////////////////////////////////////////////////////////////////////////////////////////

// Minor example: a practical case for a transaction message (e.g., this can come from an API)
// There are more complicated ways in which one can construct enums.
// For example, we can omit certain field names, or combine them with standard.
// Why would you want to do this?
// 1. Your types are well-specified
// 2.
#[derive(Debug)]
enum Request {
    Deposit(String, f64),
    Withdrawal(String, f64),
    Transfer { from: String, to: String, f64 },
    CheckBalance(String),
    PingMessage,
}

fn process_transaction(transaction: Request) {
    match transaction {
        Transaction::Deposit(account, amount) => {
            println!("Depositing ${} into account {}", amount, account);
        }
        Transaction::Withdrawal(account, amount) => {
            println!("Withdrawing ${} from account {}", amount, account);
        }
        Transaction::Transfer(from, to, amount) => {
            println!("Transferring ${} from {} to {}", amount, from, to);
        }
        Transaction::CheckBalance(account) => {
            println!("Checking balance for account {}", account);
        }
    }
}

fn main() {
    let deposit = Transaction::Deposit("Alice123", 500.0);
    let withdrawal = Transaction::Withdrawal("Bob456", 200.0);
    let transfer = Transaction::Transfer("Alice123", "Bob456", 150.0);
    let balance_check = Transaction::CheckBalance("Alice123");

    process_transaction(deposit);
    process_transaction(withdrawal);
    process_transaction(transfer);
    process_transaction(balance_check);
}

// Another extremely basic example:
enum IntOrString {
    Int(i32),
    Str(String),
}

// A very degenerate enum where there are no constructors!
// This is an important example that we will come back later in the course.
enum NoCases {

}

fn special(a: NoCases) -> A {
    match a {

    }
}

// Untyped languages are essentially typed languages!! (for example: Javascript, Python)
// But every expression has this type:
enum Anything {
    Int(i32),
    Str(String),
    Flo(f32),
    Arr(...),
    Obj(...),
    Dict(...),
}

// A simple but infinitely more useful example...
// We will come back to this example later in the course.
enum MaybeInteger {
    Yes(i32),
    Nothing,
}

fn try(a: MaybeInteger) {
    let v: i32 = match a {
        Nothing => 42,
        Yes(i) => i,
    }
    ...
}
```

## Recursive algebraic data types

Most programming languages will offer enums and structs, in one form or another.
But these two are not very expressive, since all they do is:
1. Create *combinations*, by packaging things together: to create something of type `struct` you need some data *for each* field.
2. Create *alternatives*, by offering alternative: to create something of type `enum` you need the data needed by *some* constructor.

What if you could refer to a type *while* you're defining it at the same time?
This is the same idea behind recursion, but with datatypes.
Under the hood, this is implemented using pointers, but you almost always do not have to think about it.
(The real power in programming languages comes from the use of pointers!)

```rust
enum ListIntegers {
    Empty,
    Element { value: i32, tail: ListInteger },
}
enum ListStrings {
    Empty,
    Element { value: String, tail: ListInteger },
}
// Caveat: in real Rust, tail needs to be "boxed", i.e., there is a pointer in the middle.
// Languages that have recursive algebraic data types do not really need arrays as primitives!
// Since they can be defined *inside* the language itself.
fn main() {
    // How do we create elements of these types?
    let a: ListIntegers = Empty;


    let oneElement: ListIntegers =
        Element { value: 7, tail: Empty };




    let twoElements: ListIntegers =
        Element { value: 10, tail: Element { value: 20, tail: Empty } };



    let c: ListIntegers = Element
      { value: 3, tail: Element
      { value: 7, Element
      { value: 2, tail: Empty } } };
}

// How do we use them?
// We combine `match` with recursion!

// This function takes a list and returns the total sum of the elements.
fn sum(a: ListIntegers) -> i32 {
    match a {
        Empty => 0,
        Element { value: i32, tail: ListIntegers } =>
            // This works because tail: ListInteger, so the types are correct!
            value + sum(tail)
    }
}
/*
sum(c)
= sum(Element { value: 3, tail: Element { value: 7, tail: ... } })
= 3 + sum(Element { value: 7, tail: ... })
= 3 + (7 + sum(...))
...
= 3 + (7 + (2 + sum(Empty)))
= 3 + (7 + (2 + 0))
*/
// This function takes the length of the list.
fn length(a: ListIntegers) -> i32 {
    match a {
        Empty => 0,
        Element { tail } =>
            1 + length(tail)
    }
}
fn get_tail(a: ListIntegers) -> ListIntegers {
    match a {
        Element { value, tail } => tail,
        Empty => panic!("No tail!"),
    }
}
fn get_tail_opt2(a: ListIntegers) -> ListIntegers {
    match a {
        Element { value, tail } => tail,
        Empty => Empty,
    }
}
fn get_value(a: ListIntegers) -> i32 {
    match a {
        Element { value, tail } => value,
        Empty => -1,
    }
}
fn get_value(a: ListIntegers) -> MaybeInteger {
    match a {
        Element { value, tail } => Yes(value),
        Empty => Nothing,
    }
}

// We will use the following notation for lists:
//   The list with no elements:
//       [] = Empty
//   The list with a single element a:
//       [a] = Element { value: a, tail: Empty }
//   A list with three elements a,b,c:
//       [a,b,c] = Element { value: a, tail: Element { value: b, tail: ... }}
// The convention we will use throughout the rest of the course is that the top-most
// use of the Element constructor adds the *first* element of the list.
// Another perfectly valid convention is that one considers the value `a` in
// `Element { value: a, tail ... } theto be thought as begin at the end of the list instead.

// Given a number n: i32, create the list [n, n-1, ..., 3, 2, 1]
// For example, enumerate(4) = [4,3,2,1]
fn enumerate(n: i32) -> ListInteger {
    if n <= 0 {
        Empty
    } else {
        Element {
            value: n,
            tail: enumerate(n - 1)
        }
    }
}

// WARNING: complicated example/exercise!
fn append(a: ListIntegers, b: ListIntegers) -> ListIntegers {
    match a {
        Empty => b,
        Element { value: v, tail: t } =>
          Element { value: v, tail: append(t, b) }
    }
}
```

Another example of datatypes: *trees!*

```rust
enum BinaryTreeString {
    EmptyTree,
    Node {
        left: BinaryTreeString,
        value: String,
        right: BinaryTreeString
    }
}
/*
                      <none>  <none>
                         |       |
                         +---+---+
                             |
         <none>    <none>   Emily   <none>
           |         |       |         |
           +----+----+       +----+----+
                |                 |
              Anna              Mark
                |                 |
                +--------+--------+
                         |
                       Gerry
*/
fn main() {
    let my_family: BinaryTreeStrings =
        Node {
            value: "Gerry",
            left: Node { value: "Anna", left: EmptyTree, right: EmptyTree },
            right: Node {
                value: "Mark",
                left: Node { value: "Emily", left: EmptyTree, right: EmptyTree },
                right: EmptyTree
            }
        };
}

// This function combines all the names in a genealogy tree.
fn all_names_together(a: BinaryTreeString) -> String {
    match a {
        Empty => "",
        Node { left, value, right } =>
            all_names_together(left) + value + "," + all_names_together(right)
    }
}
/*
> all_names_together(my_family)
"Anna, Gerry, Emily, Mark,"
*/

// If we had a tree of integers, this is how we could sum them:
fn sum_tree(a: BinaryTreeIntegers) -> i32 {
    match a {
        Empty => 0,
        Node { left, value, right } =>
            value + sum_tree(left) + sum_tree(right)
    }
}
// Something a bit more complicated: print the tree but also show the depth
fn all_names_with_depth(a: BinaryTreeString, depth: usize) -> String {
    match a {
        Empty => "",
        Node { left, value, right } =>
            all_names_with_depth(left, depth + 1) +
            value + " (" + depth + ")," +
            all_names_with_depth(right, depth + 1)
    }
}
/*
> all_names_with_depth(my_family, 0)
"Anna (1), Gerry (0), Emily (2), Mark (1),"
*/
```

Recursive data types are extremely powerful... you can even define integers even if you don't have them as primitive in your language!

```rust
enum PosInt {
    Zero,
    Succ { next: PosInt }
}

fn main() {
    let zero: PosInt = Zero;
    let one: PosInt = Succ { next: Zero };
    let two: PosInt = Succ { next: Succ { next: Zero } };
    let three: PosInt = Succ { next: Succ { next: Succ { next: Zero } } };
}

enum Integer {
    Pos(PosInt),
    Neg(PosInt), // we should interpret Neg(Zero) as -1 though...
}
```

## Parametric/polymorphic types

In Java they are called "generics".
The types we gave before can be defined "parametrically" for any type `A`, instead of giving them just for strings or integers:

```rust
enum List<A> {
    Empty,
    Element { value: A, tail: List<A> }
}
enum BinaryTree<A> {
    EmptyTree,
    Node {
        value: A,
        left: BinaryTree<A>,
        right: BinaryTree<A>,
    }
}

enum Either<A,B> {
    Left { value: A },
    Right { value: B }
}

struct Both<A,B> {
    left: A,
    right: B,
}

enum Option<A> {
    None,
    Some(A)
}
```

Functions also have to be defined parametrically with respect to any type.
Minor point: How does this work under the hood? Rust uses "monomorphic specialization", i.e., different
machine code is generated for each possible version of the function that gets used in your program.
