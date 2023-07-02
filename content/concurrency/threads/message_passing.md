# Using Message Passing to Transfer Data Between Threads

A popular approach to ensure safe concurrency is *message passing* where threads
communicate by sending each other messages containing data. To accomplish this,
Rust's standard library provides an implementation of *channels*. A channel is
a construct by which data is sent from one thread to another.

A channel has two halves: a *transmitter* and a *receiver*. One part of your
code calls methods on the transmitter with the data you want to send, and
another part checks the receiving end for arriving messages. A channel is said
to be *closed* if either the transmitter or receiver half is dropped.

We create a new channel using the `std::sync::mpsc::channel` function. `mpsc`
stands for *multiple producer, single consumer*. This means a channel can have
multiple sending ends that produce values but only one receiving end that
consumes those values.

The `mpsc::channel` function returns a tuple, the first element of which is the
transmitter, and the second is the receiver.

```rust
use std::thread;
use std::sync::mpsc::channel;

let (tx, rx) = channel();

let sender = thread::spawn(move || {
    tx.send("Hello, thread".to_owned())
        .expect("Unable to send on channel");
});

let receiver = thread::spawn(move || {
    let value = rx.recv().expect("Unable to receive from channel");
    println!("{}", value);
});

sender.join().expect("The sender thread has panicked");
receiver.join().expect("The receiver thread has panicked");
```

The receiver has two useful methods `recv` and `try_recv`. `recv` will block
the receiving thread's execution and wait until a value is sent down the
channel. Once a value is sent, `recv` will return it ina  `Result<T, E>`. When a
transmitter closes, `recv` will return an error to signal that no more values
will be coming.

The `try_recv` doesn't block, but will instead return a `Return<T, E>`
immediately: an `Ok` value holding a message if one is available, and an `Err`
message if there aren't any messages at this time. `try_recv` is useful when
we want to periodically check for a message (in a loop, say) while doing other
useful work.
