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
let add_one_v3 = |x|               x + 1  ;  // when called.
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

