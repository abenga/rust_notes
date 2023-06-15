# Iterators

The iterator pattern allows us to perform some tasks on a sequence of items in
turn. An iterator is responsible for the logic of iterating over each item and
determining when the sequence has finished. Iterators are *lazy* -- have no
effect until you call methods that consume the iterator to use it.

## The `Iterator` Trait and the `next` Method

All iterators implement a trait named `Iterator` that is defined in the standard
library. It's definition looks like:

```rust
pub trait Iterator {
    type Item;

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations start here ...
}
```