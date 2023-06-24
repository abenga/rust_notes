# `RefCell<T>` and the Interior Mutability Pattern

*Interior mutability* is a design pattern in Rust that allows you to mutate data
even when there are immutable references to that data. This is not normally
allowed by the borrow checker's rules. The pattern uses unsafe code inside a
data structure to bend Rust's usual rules that govern mutation and borrowing.

## Enforcing Borrowing Rules at Runtime with `Refcell<T>`

Unlike `Rc<T>`, the `RefCell<T>` type represents single ownership over the data
it holds. With references and `Box<T>`, borrowing rule invariants (at all times
we only have either one mutable reference or any number of immutable references,
and references must always be valid) are enforced at compile time. With
`RefCell<T>`, these invariants are enforced at runtime. If you break the rules,
your program will panic and exit.

Checking for adherence of these rules at compile time has two advantages:
* We catch these errors earlier in the dev cycle,
* The checks will have no impact on runtime performance.

Checking them at runtime has the advantage that certain memory-safe scenarios
will be allowed despite them probably being disallowed by compile time checks,
due to the borrow checker's inherent conservatism.

The `RefCell<T>` type is useful when you're sure your code follows the borrowing
rules but the compiler is unable to understand and guarantee that.

`RefCell<T>` is only for use in single-threaded scenarios and will give you a
compile-time error if you try to use it in a multithreaded context.