# Testing

Tests are Rust functions that verify that non-test code is functioning in the
expected manner.

The Anatomy of a Test Function

A test is a function that is annotated with the `test` attribute. Attributes
are metadata about pieces of Rust code.

```rust
pub fn real_square_root(x: f64) -> f64 {
    if x < 0.0_f64 {
        panic!("Real square root only exists for positive numbers!");
    }
    x.sqrt()
}

#[cfg(test)]
mod tests {
    use super::*;

    #[test]  // indicates this is a test function
    fn positive_square_root() {
        let result = real_square_root(4.0_f64);
        assert_eq!(result, 2.0_f64);
    }

    #[test]
    #[should_panic(expected = "Real square root only exists for positive numbers!")]
    fn negative_square_root() {
        // this test fails if it doesn't get panic we are expecting.
        real_square_root(-4.0_f64);
    }
}
```

To run tests, run `cargo test` in the crate being tested.

## Controlling How Tests Are Run

Tests are run in parallel, so we should never assume that they have shared
state - two tests should not depend on each other.

To run tests serially (or to specify some number of threads):

```shell
$ cargo test -- --test-threads=1s
```

### Showing Function Output

If a test passes, we do not see any of the output to stdout as it runs. If it
fails, we see whatever was printed to stdout + the rest of the failure message.
If we want to show printed values for passing tests as well, we do this by
passing `--show-output`

```shell
$ cargo test -- --show-output
```

### Running Specific Tests

You can specify a test by name:

```shell
$ cargo test positive_square_root
```

or match multiple tests by passing in a name that matches a set of tests, e.g.

```shell
$ cargo test square
```

will run both tests in the example above.

### Ignoring some tests

You can ignore some tests unless specifically requested by adding the 
`#[ignore]` attribute.

```rust
#[test]
fn it_works() {
    assert_eq!(2 + 2, 4);
}

#[test]
#[ignore]
fn expensive_test() {
    // code that takes a long time to run
}
```
You can run ignored tests by running:

```shell
$ cargo run -- --ignored
```

## Test Organization

*   *Unit tests*: small and more focused, testing one module at a time.
*   *Integration tests*: External to the library and use the code in the same
    way any external code would, only using the public interface.

### Unit Tests

Usually put in the src directory in each file with the code that they are
testing (see example above). It will be in a `tests` module inside each source
file. The `#[cfg(test)]` annotation on the `tests` module tells Rust to compile
and run the test only when we run `cargo test`. We are able to test private
functions inside the test module.

### Integration Tests

Integration tests go in a `tests` directory outside the `src` directory:

```
square_roots
├── Cargo.lock
├── Cargo.toml
├── src
│   └── lib.rs
└── tests
    └── integration_test.rs
```

We do not need to annotate the code in `integration_test.rs` with `#[cfg(test)]`
as Rust treats the `tests` directory and only compiles the files in this
directory when we are running `cargo test`.

```rust
use square_roots;

#[test]
fn compute_square_root_9() {
    assert_eq!(square_roots::real_square_root(9.0_f64), 3.0_f64);
}
```

Each file in the `tests` directory is a separate crate, so we need to bring the
library being tested into each crate's scope.

Tests are run in the order: unit tests, integration tests, and doc tests. If any
test in a section fails, the following sections will not be run.

If our project is a binary crate that only contains a `src/main.rs` file, we
cannot create integration tests and bring functions defined in the `main.rs`
file into scope with a `use` statement. This is why Rust projects that provide
a binary have a straightforward `src/main.rs` that calls logic that lives in a
`src/lib.rs` file.
