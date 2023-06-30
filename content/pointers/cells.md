# `RefCell<T>` and the Interior Mutability Pattern

*Interior mutability* is a design pattern in Rust that allows you to mutate data
even when there are immutable references to that data. This is not normally
allowed by the borrow checker's rules. The pattern uses unsafe code inside a
data structure to bend Rust's usual rules that govern mutation and borrowing.

## Interior Mutability: Mutating an Immutable Value

Rust memory safety is based on borrowing rule invariants. These state that given
an object `T`, at all times it is only possible to have one of the following:

*   We can any number of immutable references - *aliasing*
*   We can have either one and only one mutable reference (*mutability*)

and all references must always be valid.

These rules are usually enforced at compile time. Checking for adherence of
these rules at compile time has two advantages:

*   We catch these errors earlier in the dev cycle,
*   The checks will have no impact on runtime performance.

Checking the rules at runtime has the advantage that certain memory-safe
scenarios will be allowed despite them probably being disallowed by compile time
checks, due to the borrow checking rules not being flexible enough. Sometimes,
we would like to have multiple references to data and yet mutate it.

The `std::cell` module provides several mutable containers that exist to permit
mutability at runtime in a controlled manner:

*   `Cell<T>`
*   `RefCell<T>`
*   `OnceCell<T>`

These containers are only for use in single-threaded scenarios and will give you
a compile-time error if you try to use it in a multithreaded context. These cell
types may be mutated through shared references (i.e. the `&T` type). We say
these types provide "interior mutability", in contrast with typical Rust types
that exhibit "inherited mutability" (i.e. mutable only via `&mut T`).

### `Cell<T>`

`Cell<T>` implements interior mutability by moving values in and out of the
cell. An `&mut T` to the inner value can never be obtained, and the value itself
cannot be directly obtained without replacing it with something else. These
rules ensure that there is never more than one reference pointing to the inner
value. It is typically used for simple types where copying or moving values is
not expensive, and should be preferred over the other cell types when possible.

`Cell<T>` provides the following methods:

*   `get`: (for types that implement `Copy`) Retrieves the current interior
    value by duplicating it.
*   `take`: (for values that implement `Default`) Replaces the current interior
    value with `Default:default()` and returns the replaced value.
*   `replace`: (for all types) replaces the current interior value and returns
    the replaced value.
*   `into_inner`: (for all types) consumes the `Cell<T>` and returns the
    interior value.
*   `set`: (for all types) replaces the interior value, dropping the replaced
    value.


### `RefCell<T>`

