# Using `Box<T>` to Point to Data on the Heap

The most straightforward smart pointer is a *box*, whose type is written as
`Box<T>`. It allows you to store data on the heap rather than the stack. What
remains on the stack is the pointer to the heap data.

Boxes don't have performance overhead other than that incurred by storing data
on the heap rather than the stack. They are used:

* When you have a type whose size can't be known at compile time and you want
  to use a value of that type in a context that requires an exact size,
* When you have a large amount of data and you want to transfer ownership but
  ensure that the data won't be copied when you do so, or
* When you want to own a value and you care only that it's a type that
  implements a particular trait rather than being of a specific type.