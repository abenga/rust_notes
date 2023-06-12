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

