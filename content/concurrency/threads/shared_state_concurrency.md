# Shared-State Concurrency

Another method of handling concurrency would be for multiple threads to access
the same shared data. This sharing of data comes at a cost: we must take care
that multiple threads do not attempt to modify one variable at the same time,
or that one thread does not try to read the value of a variable while another
thread is modifying it. Updates to the resource should be *atomic*, i.e. they
should not be interrupted by another thread that simulataneously accesses the
same shared resource.

## Using Mutexes to Allow Access to Data From One Thread at a Time

To avoid the problems that can occur when threads try to update a variable at
the same time, we must use a *mutex* (abbreviation for *mutual exclusion*) to
only allow one thread to access the variable at the a given time. A mutex has
two states: *locked* and *unlocked*. At any moment, at most one thread may hold
the lock on the mutex, and a thread must first signal that it wants access by
asking to acquire the mutex's lock.

A mutex is described as *guarding* the data it holds via the locking system.
When using a mutex:

*   You must attempt to acquire the lock before using the data.
*   When you're done with the data the mutex guards, you must unlock the data
    so other threads can acquire the lock.

Thanks to Rust's type system and ownership rules, you can't get locking and
unlocking wrong.

## The API of `Mutex<T>`

We create a `Mutex<T>` using the associated function `new`. To access the data
inside the mutex, we use the `lock` method to acquire the lock. The call will
block the current thread so it can't do any work until it's our turn to have the
lock.

```rust
use std::sync::Mutex;

let m = Mutex::new(5);

{
    let mut num = m.lock().unwrap();
    *num = 6;
}

println!("m = {:?}", m);
//>>> m = Mutex { data: 6, poisoned: false, .. }
// Inner value has been changed.
```

## Sharing a Mutex Between Threads

We can share a `Mutex<T>` between threads, as in the example below:

```rust
use std::sync::{Arc, Mutex};
use std::thread;

let counter = Arc::new(Mutex::new(0));
let mut handles = vec![];

for _ in 0..10 {
    let counter = Arc::clone(&counter);
    let handle = thread::spawn(move || {
        let mut num = counter.lock().unwrap();
        *num += 1;
    });
    handles.push(handle);
}

for handle in handles {
    handle.join().unwrap();
}

println!("Result: {}", *counter.lock().unwrap());
```