# Module System

Features that consist the module system:

* Workspaces
* Packages
* Crates
* Modules
* Paths

## Crates

A *crate* is the smallest amount of code that the Rust compiler considers at a
time. Crates can contain modules. A crate can be a *binary crate* or a *library
crate*. Binary crates contain a `main` function are programs compiled to an 
executable that can be run. Library crates don't have a `main` function and 
are used to define functionality to be shared with multiple projects.

## Packages

A *package* is a bundle of one or more crates that provides a set of 
functionality. A package contains a Cargo.toml file that describes how to build
those crates. 

A package may contain several binary crates, but at most only one library crate.
A package must contain at least one crate.

## Modules

Modules let us organize code within a crate for readability and easy reuse while
controlling the privacy of items.

* Compiler looks in crate root file (`src/lib.rs` or `src/main.rs`) for code to
  compile.

* In crate root file, we can declare new modules as follows:

```rust
// In park/src/main.rs
mod animals; // Compiler looks (in order):
             // * for an inline `mod animals {}` block
             // * in the file src/animals.rs
             // * in the file src/animals/mod.rs
```

* Modules can have submodules:

```rust
// in park/src/animals.rs
// pub keyword makes a module public (visible to its parent modules). Modules
// are private by default.
pub mod cats; // Compiler looks (in order):
              // * inline for a `mod cats {}` block
              // * in the file src/animals/cats.rs
              // * in the file src/animals/cats/mod.rs
              //
              // e.g. find `Lion` type at `crate::animals::cats::Lion`
              // `use` creates shortcuts, e.g. if we added
              // use crate::animals::cats::Lion to some scope, we only need to 
              // write Lion to use the type in that scope.
```

We may bring paths into scope with the `use` keyword in order to avoid having
the inconvenience and repetetiveness of writing out full paths to use types or
call functions. We can reexport a name from a scope by using `pub use`.

We can also bring names with an alias using the `as` keyword. For example:

```rust
use std::fmt::Result;
use std::io::Result as IoResult;
```

Use nested paths to bring several paths into scope in one line

```rust
use std::{cmp::Ordering, io};
```

Use the glob operator `*` brings all public items defined in a path into scope.