# Using `Box<T>` to Point to Data on the Heap

The most straightforward smart pointer is a *box*, whose type is written as
`Box<T>`. It allows you to store data on the heap rather than the stack. What
remains on the stack is the pointer to the heap data.

Boxes don't have performance overhead other than that incurred by storing data
on the heap rather than the stack. They are used:

* When you have a type whose size can't be known at compile time and you want
  to use a value of that type in a context that requires an exact size,
* When you have a large amount of data and you want to transfer ownership but
  ensure that the data won't be copied when you do so, or
* When you want to own a value and you care only that it's a type that
  implements a particular trait rather than being of a specific type.

## Using a `Box<T>` to Store Data on the Heap

Storing an `i32` value on the heap:

```rust
let b = Box::new(5);  // the value 5 is allocated on the heap.

println!("b = {}", b); //>>> b = 5
// after b goes out of scope, both the box (the pointer on the stack)
// and the data it points to on the heap are deallocated.
```

## Enabling Recursive Types with Boxes

A value of *recursive type* can have another value of the same type as part of
itself. These are challenging because at compile time Rust needs to know how
much space a type takes up. Nesting of values of recursive types could in
theory continue infinitely, so Rust can't know how much space the value needs.
Because boxes have a known isze, we can enable recursive types by inserting a
box in the recursive type definition.

For example, to implement a *cons list* (Lisp version of a linked list).

```rust
enum List {
    Cons(i32, Box<List>),
    Nil,
}

use crate::List::{Cons, Nil};

fn main() {
    let list = Cons(1, Box::new(Cons(2, Box::new(Cons(3, Box::new(Nil))))));
}
```

Boxes provide only indirection and heap allocation; they do not have any other
special capabilities, like those we will see with other smart pointer types.
They also don't have any performance overhead, so they are useful in cases when
indirection is all we need.
