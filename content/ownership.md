# Ownership

Ownership is a feature of Rust that allows us to make memory safety guarantees
without needing a garbage collector. This enables Rust programs to never have
undefined behavior. The checks described here are done at compile time, not at
run time.

## Stack vs Heap Variables

Every program has two pools of memory: the *stack* and the *heap*.

The stack is an area of memory in which we add values, and can remove the last
value that was added (pushed) onto the stack as functions are called and return. 
We usually store primitive variable data owned by a function's local variables
on the stack.

The heap is an area of memory that is used to store data types that can grow and
shrink like strings, vectors, etc, and values that can live longer than a single
function's execution. We can use variables on the stack to refer to data on the
heap using *pointers*.

A box (`Box<T>`) provides the simplest form of heap allocation in rust.

```rust
let val: u8 = 5;
let boxed: Box<u8> = Box::new(val);  // Moves a value from the stack to the heap

let boxed_2 = Box<u8> = Box::new(5);
let val: u8 = *boxed; // Move a value from a box back to the stack by dereferencing.
```

## What is Ownership?

The ownership system of Rust is a system that ensures that exactly one variable
binding is bound to a given defined resource at any one time, i.e. it *owns*
that resource.

Rust has two types of data types: *copy types* and *move types*. They behave 
distinctly from each other when a variable is assigned to a new variable.

### Copy Types

Copy variables are copied when assigned to a new variable. Primitive variables
(characters, integer types, float types, booleans, tuples of primitive types, 
and arrays of primitive types) are among copy types. These are data types that 
implement the `Copy` trait. More types that implement the trait by default are 
listed in the [trait's documentation page](
https://doc.rust-lang.org/std/marker/trait.Copy.html).

```rust
let x: u8 = 3;
let y = x;
println!("{x}, {y}");  // Both x and y have the value 3 and can be read.
//>>> 3, 3
```

Copy types are stored directly on the stack. They are generally small, and their
size is known at compile time, so they can be copied quickly and cheaply.

### Move Types

Move types are non-primitive types. Their data is stored on the heap, and the
stack stores a pointer to the data on the heap (plus some other book-keeping
information, e.g. size and capacity of the data on the heap).

Because move types are generally costlier than copy types to copy, we "move",
rather than copy them. This means that when a variable is reassigned, the
original variable loses ownership, and the pointer to the data is moved to the
new variable. After this, we can no longer access the original variable.

When the variable goes out of scope, the bound resources are freed. If we assign
the value to another variable binding, ownership of the resource is *moved* to
the new variable binding, and the original variable becomes invalid.

```rust
// Assignments or passing function arguments by value "move" the resource from 
// the original owner (x, in the assignmentexample below) to the new owner (y,
// in this case).
let x = vec![1, 45, 43];
let y = x;

println!("{:?}", y);
// println!("{:?}", x); // commenting this out makes the code fail to compile, 
//                      // as x no longer owns the vector and isn't accessible.
```