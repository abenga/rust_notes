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

To use lifetime annotations in function signatures, we need to add generic
lifetime parameters inside angle brackets between the function name and the
parameter list. Lifetime annotations begin with an apostrophe (`'`) and are
usually all lowercase and short (usually single letters like type annotations)
with a space to separate the annotation from the reference's types.

```rust
fn longest<'a>(x: &'a str, y: &'a str) -> &'a str {
    if x.len() > y.len() {
        x
    } else {
        y
    }
}
```