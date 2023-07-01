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