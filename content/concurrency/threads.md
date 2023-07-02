# Threads

Threads are a mechanism that enables a program to perform multiple tasks
concurrently. Each thread represents a single logical control flow, executing
the same program independently, sharing the same global memory.

Threads are sometimes preferrable to proceses because it addresses some
limitations of processes:

*   Sharing data between threads is easy and fast (we just need to write it into
    shared variables). We do need to synchronize access.
*   Thread creation is usually faster than process creation.

Splitting program computation into multiple threads can improve performance, but
it adds complexity. Because threads canrun simultaneously, there's no guarantee
about the order in which the parts of the control flow in threads wil run. This
may cause:

*   *Race conditions*, where threads access data or resources in an inconsistent
    order.
*   *Deadlocks*, where two threads are waiting for each other, preventing both
    threads from continuing.
*   Hard to reproduce bugs, where errors occur because operations in threads
    run in a certain order.

As such, multithreaded programming requires careful thought and requires a code
structure that is different from single-threaded code.

The Rust standard library uses a 1:1 model of thread implementation, where a
program uses one operating system thread per one language thread. There are
crates that implement other models of threading that make different tradeoffs to
the 1:1 model.

## Creating a New Thread with `spawn`

To create a new thread, we call the `std::thread::spawn` function and pass it a
closure containing the code we want to run in the new thread.

```rust
use std::thread;

thread::spawn(|| {
    // some code to run...
});
```

## Waiting for All Threads to Finish using `join` Handles

When the main thread of a Rust program completes, all spawned threads are shut
down, whether or not they have finished running. This may stop some threads
prematurealy, or prevent threads from running at all.

We can fix this problem by saving the return value of the call to `spawn` in a
variable (of type `JoinHandle`). This is an owned value that when we call the
`join` method on it, will wait for its thread to finish.

```rust
use std::thread;

let handle = thread::spawn(|| {
    // do some computations
});

handle.join().unwrap();  // if the spawned thread panics, `join` will return 
                         // an `Err` containing the argument given to `panic!`
```

Calling `join` on the handle blocks the thread currently running until the
thread represented by the handle terminates. We have to be careful where we call
`join`, as this can affect whether or not threads run at the same time.

## Using `move` Closures with Threads

We'll often use the `move` keyword with closures passed to `thread::spawn`
because the closure will then take ownership of the values it uses from the
environment, thus transferring ownership of those values from one thread to
another.

```rust
use std::thread;

let v = vec![1, 2, 3];

let handle = thread::spawn(move || {
    println!("Vector: {:?}", v);
});
```
