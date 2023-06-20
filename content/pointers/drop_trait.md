# Running Code on Cleanup with the `Drop` Trait

The `Drop` trait lets us customize what happens when a value is about to go out
of scope. You can provide an implementation for the `Drop` trait on any type,
and it can be used to release resources like files or network connections. For
exampel, when a `Box<T>` is dropped, it will deallocate the space on the heap
that the box points to. The compiler inserts this code automatically, and every
value that goes out of scope will be cleaned up, and we won't leak resources.

```rust
struct CustomSmartPointer {
    data: String,
}

impl Drop for CustomSmartPointer {
    fn drop(&mut self) {
        // code to run when going out of scope.
    }
}
```

The `Drop` trait is included in the prelude, so we do not need to bring it into
scope.

## Dropping a Value Early with `std::mem::drop`

