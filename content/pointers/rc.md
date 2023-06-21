# `Rc<T>`, the Reference Counted Smart Pointer

There are cases when a single value may have multiple owners, for example in
graph data structures where multiple edges might point to the same node, and
that node is conceptually owned by all the edges that point to it. A node
shouldn't be cleaned up unless it doesn't have any edges pointing to it and so
has no owners. You have to enable multiple ownership explicitly by using the
Rust type `Rc<T>`, which is an abbreviation for *reference counting*. The
`Rc<T>` type keeps track of the number of references to a value to determine
whether or not the value is still in use.

We use the `Rc<T>` type when we want to allocate some data on the heap for
multiple parts of our program to read and we can't determine at compile time
which part will finish using the data last. If we knew which part would finish
last, we could just make that part the data's owner, and use normal ownership
rules to clean it up when that part goes out of scope.

`Rc<T>` is only for use in single-threaded scenarios.

## Using `Rc<T>` to Share Data

```rust
enum List {
    Cons(i32, Rc<List>),
    Nil,
}

use crate::List::{Cons, Nil};
use std::rc::Rc;  // Rc is not in the prelude

fn main() {
    let a  = Rc::new(Cons(5, Rc::new(Cons(10, Rc::new(Nil)))));
    let b = Cons(3::Rc::clone(&a));
    let c = Cons(4: Rc::clone(&a));
}
```

The calls to `Rc::clone(&ref)` do not make a deep copy of all the data. It only
increments the reference count, which is quick. We use `Rc::clone` to make it
easy to distinguish between the deep-copy clones and the clones that only
increase the reference count.
