# Validating References with Lifetimes

Every reference in Rust has a *lifetime*, which is the scope for which the
reference is valid. Usually, they are implicit and inferred, starting when the
variable is declared and ending when it goes out of scope. When inferring the
lifetime of variables is not straightforward, Rust allows (more like requires)
us to include information about lifetimes using *lifetime annotations*, added
as generic lifetime parameters to ensure actual references used at runtime will
be valid.

## Data Should Outlive Its References

```rust
fn main() {
    let r;  // lifetime 'a begins

    {  // lifetime 'b begins
        let x = 5; // x has lifetime 'b
        r = &x;
    } // lifetime 'b ends

    println!("r: {}", r);  // invalid: references x which only exists in 'b
                           // which has ended. It is a "dangling reference".
} // lifetime 'a ends
```

To fix, remove inner scope:

```rust
fn main() {
    let x = 5;
    let r = &x;
    println!("r: {}", r);
}
```

## Generic Lifetimes in Functions

This function:

```rust
fn longest(x: &str, y: &str) -> &str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

would not compile because Rust cannot tell whether the reference returned refers
to `x` or `y`, so it is unsure about whether this reference should live as long
as `x` or `y`, depending on whether the `if` block or the `else` block executes.
We need to add generic lifetime parameters to provide this information to the
borrow checker for its analysis.

## Lifetime Annotation Syntax

### Lifetime Annotations in Function Signatures

To use lifetime annotations in function signatures, we need to add generic
lifetime parameters inside angle brackets between the function name and the
parameter list. Lifetime annotations begin with an apostrophe (`'`) and are
usually all lowercase and short (usually single letters like type annotations)
with a space to separate the annotation from the reference's types.

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    // Annotations above mean that the lifetime of the reference returned
    // from the function is the same as the smaller of the lifetimes of the
    // values referred to as the function arguments. The compiler will reject
    // any arguments that cannot satisfy this signature.
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

### Lifetime Annotation in Struct Definitions

Sometimes we need structs to hold references. In this case, we would need to add
a lifetime annotation on every reference in the struct's definition.

```rust
struct ImportantExcerpt<'a> {
    part: &'a str,
}
```

## Lifetime Elision

The compiler uses three rules to figure out the lifetimes of references that
aren't explicitly annotated. These rules apply to `fn` definitions and `impl`
blocks.

1.  The compiler assigns a different lifetime parameter to each lifetime in each
    input type. For example:
    *   `fn foo(x: &i32)` gets one lifetime parameter and becomes 
        `fn foo<'a'>(x: &'a i32)`.
    *   `fn foo(x: &i32, y: &i32)` would get two lifetime parameters and becomes
        `fn foo<'a, 'b>(x: &'a i32, y: &'b i32)`.
    *   The function `fn foo(x: &ImportantExcerpt` would get two lifetime 
        parameters and become `fn foo<'a, 'b>(x: &'a ImportantExcerpt<'b'>)`.

2.  If there is exactly one input lifetime parameter, that lifetime is assigned
    to all output lifetime parameters: `fn foo<'a'>(x: &'a i32) -> &'a i32`.

3.  If there are multiple input lifetime parameters, but one of them is `&self`
    or `&mut self` because this is a method, the lifetime of `self` is assigned
    to all output lifetime parameters.

If the compiler applies all the rules and it still cannot figure out the
lifetimes of some references, the compiler will stop with an error.

## The Static Lifetime

The `'static` lifetime denotes that the affected reference can live for the
entire duration of the program. String literals are stored directly in the
program's binary, and are always available. They have the `'static` lifetime.

## Generic Type Parameters, Trait Bounds, and Lifetimes Together

Example of a function that has generic type parameters, trait bounds, and
lifetimes:

```rust
use std::fmt::Display;

fn longest_with_an_announcement<'a, T>(
    x: &'a str,
    y: &'a str,
    ann: T,
) -> &'a str
where
    T: Display,
{
    println!("Announcement! {}", ann);
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```

