# Closures

Closures are anonymous functions that capture their environment.They can be
saved in a variable or passed as arguments to other functions. You can create a
closure in one place and then call it elsewhere to evaluate it in a different
context.

## Closure Type Inference and Annotation

It is usually not necessary to annotate the types for a closure. For example,
the following illustrates the similarity between closure syntax and function
syntax except for the use of types and the amount of syntax that is optional:

```rust
fn  add_one_v1   (x: u32) -> u32 { x + 1 }   // Function definition.

let add_one_v2 = |x: u32| -> u32 { x + 1 };  // These closure definitions
let add_one_v3 = |x|             { x + 1 };  // all produce the same behaviour
let add_one_v4 = |x|               x + 1  ;  // when called.
```

For closure definitions, the compiler will infer one concrete type for each of
their parameters and for their return value depending on the types of the
parameters and the return type.

## Capturing References or Moving Ownership

Closures can capture values from their environment in three ways: borrowing
immutably, borrowing mutably, and taking ownership. The closure decides which
of these to do based on what its body does with the captured values.

* **Borrowing immutably**

```rust
let list = vec![1, 2, 3];
println!("Before defining closure: {:?}", list);

let only_borrows = || println!("From closure: {:?}", list);

println!("before calling closure: {:?}", list);
only_borrows();
println!("after calling closure: {:?}", list);
```

* **Borrowing mutably**

```rust
let mut list = vec![1, 2, 3];
println!("before defining closure: {:?}", list);

let mut borrows_mutably = |x| list.push(x);

borrows_mutably(4);
borrows_mutably(5);
println!("after calling closure: {:?}", list);
//>>> after calling closure: [1, 2, 3, 4, 5]
```

* **Taking ownership of values**

We move data into a closure by using the `move` keyword before the parameter
list.

```rust
let list = vec![1, 2, 3];
println!("before defining closure: {:?}", list);

let mut moves_into_closure = move || println!("{:?}", list);

moves_into_closure();
println!("after calling closure: {:?}", list);
```

This is mostly useful when passing a closure to a new thread to move the data so
that it is owned by the new thread.

```rust
use std::thread;
// ...
thread::spawn(move || println!("from thread: {:?}", list))
    .join()
    .unwrap();
```

## Moving Captured Values Out of Closures and the `Fn` Traits

Depending on what is defined in the body of a closure, it can move a captured
value out of the closure, mutate the captured value, neither move nor mutate
the value, or capture nothing from the environment to begin with. The way the
closure captures and handles values from the environment affects which traits
the closure implements, and traits are how functions and structs specify what
kinds of closures they can use. Closures will automatically implement one, two,
or three of these `Fn` traits in additive fashion depending on how the closure's
body handles the values:

### `FnOnce`

[`FnOnce`](https://doc.rust-lang.org/std/ops/trait.FnOnce.html) applies to all
closures that can be called once. All closures implement at least this trait,
because all closures can be called. A closure that moves captured values out of
its body will only implement `FnOnce` and none of the other `Fn` traits because
it can only be called once.

We should use `FnOnce` as a bound when we want to accept a parameter of
function-like type and only need to call it once.

```rust
fn consume_with_relish<F>(func: F)
    where F: FnOnce() -> String
{
    println!("Consumed: {}", func());
    println("Delicious!");
}

let x = String::from("Hello");
let consume_and_return_x = move || x;
consume_with_relish(consume_and_return_x)

// consume_and_return_x can no longer be invoked at this point.
```

### `FnMut`

[`FnMut`](https://doc.rust-lang.org/std/ops/trait.FnMut.html) applies to
closures that don't move captured values out of their body, but that might
mutate the captured values. These closures can be called more than once. `FnMut`
is implemented automatically by closures that take mutable references to
captured variables, as well as all types that implement `Fn` (since `FnMut` is
a supertrait of `Fn`).

Use `FnMut` when you want to accept a parameter of function-like type and need
to call it repeatedly, while allowing it to mutate state.

```rust
let mut x = 5;
{
    let mut square = || x *= x;
    square(x)
}
println!(x);
//>>> 25
```

using an `FnMut` parameter:

```rust
fn do_twice<F>(mut func: F)
    where F: FnMut()
{
    func();
    func();
}

let mut x = 5;
{
    let mut square = || x *= x;
    do_twice(square);
}
println!("{}", x);
//>>> 625
```

### `Fn`

[`Fn`](https://doc.rust-lang.org/std/ops/trait.Fn.html) applies to closures that
don't move captured values out of their body and don't mutate captured values,
as well as closures that capture nothing from their environment. These closures
can be called more than once without mutating their environment, which is
important in cases such as calling a closure multiple types concurrently.

Since both `FnMut` and `FnOnce` are supertraits of `Fn`, any instance of `Fn`
can be used as a parameter where a `FnMut` or `FnOnce` is expected.

Calling an instance of `Fn`:

```rust
let square = |x| x * x;
println!("square(5) = {}", square(5));
//>>> square(5) = 625
```

Using a `Fn` parameter:

```rust
fn call_with_five<F>(func: F) -> usize
    where F: Fn(usize) -> usize
{
    func(5)
}

let square = |x| x * x;
println!("square(5) = {}", call_with_five(square));
//>>> square(5) = 625
```

Note that functions can implement all three of the `Fn` traits too. If what we
want to do doesn't require capturing a value from the environment, we can use
the name of a function rather than a closure when we need something that
implements one of the `Fn` traits, e.g. calling `unwrap_or_else(Vec::new)` when
we want to get a new empty vector when a value is `None`.
