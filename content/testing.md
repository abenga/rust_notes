# Testing

Tests are Rust functions that verify that non-test code is functioning in the
expected manner.

The Anatomy of a Test Function

A test is a function that is annotated with the `test` attribute. Attributes
are metadata about pieces of Rust code.

```rust
pub fn add(left: usize, right: usize) -> usize {
    left + right
}

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
    fn test_correct_add() {
        let result = add(2, 2);
        assert_eq!(result, 4);
    }

    #[test]
    #[should_panic(expected = "Real square root only exists for positive numbers!")]
    fn test_negative_square_root() {
        // this test fails if it doesn't get panic we are expecting.
        real_square_root(-4.0_f64);
    }
}
```