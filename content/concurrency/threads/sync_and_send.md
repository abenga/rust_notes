# Extensible Concurrency with the `Sync` and `Send` Traits

There are two concurrency concepts embedded in rust, the `std::marker` traits
`Sync` and `Send`.

## `Send`

The `Send` marker trait indicates that ownership of values of the type
implementing `Send` can be transferred between threads. Most Rust types are
`Send`, but there are some exceptions, including `Rc<T>`: which cannot be `Send`
because if you cloned an `Rc<T>` value and tried to transfer ownership of the
clone to another thread, both threads might update the reference count at the
same time. In case we need to transfer a reference between threads, we use
`Arc<T>`, which is `Send`.

Any type composed entirely of `Send` types is automatically marked as `Send` as
well. Most primitive types are `Send`, aside from raw pointers.

## `Sync`

The `Sync` marker trait indicates that it is safe for the type implementing
`Sync` to be referenced from multiple threads. A type `T` is `Sync` if `&T`
(an immutable reference to `T`) is `Send`, meaning the reference can be sent
safely to another thread. Primitive types are `Sync`, and types composed
entirely of types that are `Sync` are also `Sync`.

The reason for having separate `Send` and `Sync` traits is that a type can be
one, or both, or neither.

*   The smart pointer `Rc<T>` is neither `Send` nor `Sync`.
*   The `RefCell<T>` type and the family of related `Cell<T>` types are `Send`,
    but they are not `Sync`. A `RefCell` can be sent across a thread boundary,
    but not accessed concurrently, because the implementation of borrow checking
    that `RefCell<T>` does at runtime is not thread-safe.
*   The smart pointer `Mutex<T>` is `Send` and `Sync`, and can be used to share
    access with multiple threads.
*   The type `MutexGuard<'a T>` that is returned by `Mutex::lock` is `Sync` (if
    `T: Sync`) but not `Send`. It is specifically not `Send` because some
    platforms mandate that mutexes are unlocked by the same thread that locked
    them.

Implementing `Send` and `Sync` manually is unsafe. Because types that are made
up of `Send` and `Sync` traits are automatically also `Send` and `Sync`, we do
not have to implement thos traits manually. As marker traits, they do not have
any methods to implement. Building new concurrent types not made up of `Send` &
`Sync` parts requires careful thought to uphold thread-safety guarantees.