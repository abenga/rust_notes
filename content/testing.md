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

