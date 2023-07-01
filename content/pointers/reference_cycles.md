# Reference Cycles & Memory Leaks

Rust's memory safety guarantees make it difficult, but not impossible, to
accidentally create memory that is never cleaned up (a *memory leak*). Using
`Rc<T>` and `RefCell<T>`, it is possible to create references where items refer
refer to each other in a cycle. This creates memory leaks because the reference
count of each item in the cycle will never reach 0, and the values will never be
dropped.
