# Shared-State Concurrency

Another method of handling concurrency would be for multiple threads to access
the same shared data. This sharing of data comes at a cost: we must take care
that multiple threads do not attempt to modify one variable at the same time,
or that one thread does not try to read the value of a variable while another
thread is modifying it. The term *critical section* is used ot refer to a
section of code that accesses a shared resource and whose execution should be
*atomic*, i.e. its execution should not be interrupted by another thread that
simulataneously accesses the same shared resource.
