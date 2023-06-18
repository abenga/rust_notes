# Treating Smart Pointers Like Regular References with the `Deref` Trait

Implementing the `Deref` trait allows you to customize the behavior of the
*dereference operator* `*`. By implementing `Deref` in such a way that a smart
pointer can be treated like a regular reference, you can write code that
operates on references and use that code with smart pointers too.

## Following the Pointer to the Value

A regular reference is a type of pointer. To get the value that a reference
points to, we use the `*` operator to follow the reference to the value it's
pointing to (hence *dereference*).

```rust
let x = 5;
let y = &x;  // pointer to the location of `x`

assert_eq!(5, x);
assert_eq!(5, *y);
```

## Using `Box<T>` Like a Reference

The code listing above can also use a `Box<T>` instead of a reference.

```rust
let x = 5;
let y = Box::new(x);  // pointer to a copied value of `x`

assert_eq!(5, x);
assert_eq!(5, *y);  // dereference operator can follow the pointer of the
                    //`Box<T>` jjust the same as when it was a reference.
```

## Defining a New Smart Pointer

```rust
use std::ops::Deref;

struct MyBox<T>(T);

impl<T> MyBox<T> {
    fn new(x: T) -> MyBox<T> {
        MyBox(x)
    }
}

impl<T> Deref for MyBox<T> {
    type Target = T;

    fn deref(&self) -> &Self::Target {
        &self.0 // return a reference to the value we want to access, so that
                // deref operator returns the value we want to acess with `*`.
    }
}

fn main() {
    let x = 5;
    let y = MyBox::new(x);  // pointer to a copied value of `x`

    assert_eq!(5, x);
    assert_eq!(5, *y); // this is actually `*(y.deref())`
}
```

Without the `Deref` trait, the compiler can only dereference `&` references. The
`deref` method gives the compiler the ability to take a value of any type that
implements `Deref` anc call the `deref` method to get a `&` reference that it
knows how to dereference.

The `deref` method returns a reference to a value so that the plain dereference
outside the parentheses `*(y.deref())` is still necessary because of the
ownership system. If the `deref` method returned the value directly instead of a
reference to the value, the value would be moved out of `self`. We usually do
not want to take ownership of the inner value inside `MyBox<T>` in most cases
when we use the dereference operator.

## Implicit Deref Coercions with Functions and Methods

*Deref coercion* converts a reference to a type that implements the `Deref`
trait into a reference to another type. For example, deref coercion can convert
a `&String` to `&str` because `String` implements the `Deref` trait such that
it returns `&str`. It happens automatically when we pass a reference to a
particular type's value as an argument to a funciton or method that doesn't
match the parameter type in the function or method definition. A sequence of
calls to the `deref` method converts the type we provided into the type the
parameter needs.

```rust
fn hello(name: &str) {
    println!("Hello, {name}");
}

fn main() {
    let m = MyBox::new(String::from("Rust"));
    hello(&m);  // &m is a reference to a `MyBox<String>`. Rust turns the
                // &MyBox<String> into `&String` by calling `deref`.
                // The standard library provides an implementation of `Deref`
                // on `String` that returns a string slice. Rust calls `deref`
                // again to turn the `&String` into `&str`, which matches the
                // `hello` functions definition.
}
```

If Rust didn't implement deref coercion, we would have to explicitly write
`hello(&(*m)[..])`.

In addition to the convenience of not needing to explicitly write all these
dereferences, deref coercion allows us to writemore code that can work for both
references and smart pointers.
