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
    type Item;  // Associated type (see: generics/traits/associated_types).
                // this is the type returned from the iterator.

    fn next(&mut self) -> Option<Self::Item>;

    // methods with default implementations start here ...
}
```

The `Iterator` trait only requires implementors to define one method: the `next`
method, which returns one item of the iterator at a time wrapped in `Some`, and
when the iteration is over, returns `None`.

```rust
let v1 = vec![1, 2, 3];
let mut v1_iter = v1.iter();  // v1_iter needs to be mutable as calling `next`
                              // changes the internal state that the iterator
                              // uses to keep track of its location in `v1`.

println!("{:?}", v1_iter.next());  //>>> Some(1)   > These are immutable 
println!("{:?}", v1_iter.next());  //>>> Some(2)   > references to the values
println!("{:?}", v1_iter.next());  //>>> Some(3)   > in the vector
println!("{:?}", v1_iter.next());  //>>> None
```

If we want to create an iterator that takes ownership of `v1` and returns owned
values, we call `into_iter` instead of `iter`, and if we want to iterate over
mutable references, we can call `iter_mut` instead of `iter`.

## Methods that Consume the Iterator

Methods that call `next` on an iterator are called *consuming adaptors* because
calling them uses up the iterator.

```rust
fn iterator_sum() {
    let v1 = vec![1, 2, 3];
    let v1_iter = v1.iter();

    let total: i32 = v1_iter.sum();
    // cannot use v1_iter after the call to `sum` because `sum` takes 
    // ownership of the iterator we call it on.

    println!("{}", total);
    //>>> 6
}
```

## Methods that Produce Other Iterators

*Iterator adaptors* are methods defined on the `Iterator` trait that don't
consume the iterator. Instead, they produce different iterators by changing some
aspect of the original iterator.

```rust
let v1 = vec![1, 2, 3];

let v2: Vec<_> = v1.iter().map(|x| x * x).collect();

println!("{:?}", v2);  //>>> [1, 4, 9]
println!("{:?}", v1);  //>>> [1, 2, 3]
```

You can chain multiple calls to iterator adaptors to perform complex actions in
a readable way. But because all iterators are lazy, you have to call one of the
consuming adaptor methods to get results from calls to iterator adaptors.

## Using Closures that Capture Their Environment

