# Concurrent Programming

A computer processor when running usually runs a sequence of instructions
retrieved from the program counter. What instruction to run next is usually
serial (run instructions adjacent in memory, one after the other), but could
move elsewhere due to jumps, function calls, and function returns.

A sequence of such instructions is called a *control flow*. When logical control
flows overlap in time, we say they are *concurrent*. The operating system
usually runs multiple logical control flows concurrently in order to run
multiple programs at the same time, usually by switching between them.

We can write programs that use application-level concurrency. These programs are
called *concurrent programs*. This is desirable in order to:

*   Do useful work while waiting for slow I/O devices.
*   Handle input instructions from users that do not necessarily wait for the
    last one to complete, e.g. continuing to view and read a document while
    printing it.
*   Defer work in order to reduce latency, e.g. buffering writes to a file and
    writing a larger batch at some point in the future.
*   Serving multiple network clients, e.g. a web or database server will need to
    respond to queries from multiple users at the same time.
*   Parallel computing on multiprocessor machines.

Concurrent programs may be written using these approaches:

*   ***Processes***: In this approach, each logical control flow is a process
    that is scheduled and maintained by the kernel. Because multiple processes
    have separate memory address spaces, processes that want to communicate with
    each other need to use some *interprocess communication* (IPC) mechanism.

*   ***I/O Multiplexing***: In this approach, applications explicitly schedule
    their own logical flows in the context of a single process. The program
    execution transitions as a result of data arriving on file descriptors. All
    flows share the same address space.

*   ***Threads***: Threads are logical flows that run in the context of a single
    process and are scheduled by the kernel. They share the same virtual address
    space like I/O multiplexing flows.

Concurrent programming and parallel programming are becoming increasingly
important as more computers take advantage of multiple processors. Programming
in this context is difficult and error prone, but Rust hopes to change this. By
leveraging ownership and type checking, many concurrency errors are compilation
errors rather than runtime errors. This allows us to write code that is free of
subtle bugs and is easy to refactor without introducing new bugs.
