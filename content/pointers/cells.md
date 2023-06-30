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

```rust
use std::cell::Cell;

let a: Cell<u8> = Cell::new(8);

let b = a.get();
println!("{}, {:?}", b, a);  //>>> 8, Cell { value: 8 }

let c = a.replace(16);
println!("{}, {:?}", c, x);  //>>> 8, Cell { value: 16 }

a.set(32);
println!("{:?}", x);  //>>> Cell { value: 32 }

let d = a.into_inner(); // we cannot access `a` after this, it is consumed.
println!("{}", d);  //>>> 32
```

### `RefCell<T>`

`RefCell<T>` uses Rust's lifetimes to implement "dynamic borrowing", a process
by which we claim temporary, exclusive, mutable access to the inner value. These
borrows are tracked at runtime. `RefCell<T>` does this by providing the methods:

*   `borrow`: enables us to obtain an immutable reference to a `RefCell`'s inner
    value (`&T`). The borrow lasts until the returned reference exits scope.
    It panics if the value is currently mutably borrowed.
*   `borrow_mut`: enables us to perform a mutable borrow (`&mut T`). The borrow
    lasts until the retuned mutable reference or all mutable references derived
    from it exit scope. The value cannot be borrowed again while this borrow is
    active. If the value is already borrowed, the thread will panic.
*   `try_borrow`: Immutably borrows the wrapped value, returning an error if the
    value is currently mutably borrowed. The borrow lasts until the `Ref` exits
    scope.
*   `try_borrow_mut`: Mutably borrows the wrapped value, returning an error if
    the value is currently borrowed. The borrow lasts until the `Ref` exits
    scope.

Immutable borrows:

```rust
use std::cell::RefCell;

let c = RefCell::new(5);

let borrowed = c.borrow();
let borrowed2 = c.borrow();
println!("{}, {}", borrowed, borrowed2);
//>>> 5, 5
```

Mutable borrow:

```rust
let c = RefCell::new(5);
println!("{:?}", c);  //>>> RefCell { value: 5 }
{
    let mut borrowed_mut = c.borrow_mut();
    *borrowed_mut += 1;
}
println!("{:?}", c);  //>>> RefCell { value: 6 }
```

Failed mutable borrow:

```rust
use std::cell::RefCell;

let c = RefCell::new(5);

let borrowed = c.borrow();
let mut borrowed_mut = c.borrow_mut(); // This causes a panic
//>>> thread 'main' panicked at 'already borrowed: BorrowMutError', src/main.rs:17:34
//>>> note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

Non-panicking variant of `borrow`:

```rust
use std::cell::RefCell;

let c = RefCell::new(5);

let mut borrowed_mut = c.borrow_mut();
let borrowed = c.try_borrow();
println!("{:?}", borrowed);  //>>> Err(BorrowError)
```

`try_borrow_mut` does something similar, only returning `Err(BorrowMutError)`.
