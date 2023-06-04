# Traits: Defining Shared Behaviour

A *trait* defines functionality a particular type has and can share with other
types.

## Defining a Trait

We declare a trait using the `trait` keyword then the trait's name. In curly
brackets, we declare the method signatures that describe the behavior of the
types that implement the trait.

```rust
pub trait Shape {
    fn area(&self) -> f64;
    fn perimeter(&self) -> f64;
}
```

## Implementing a Trait on a Type

Let's define a `Shape` trait that defines an `area` method.

```rust
pub trait Shape { fn area(&self) -> f64; }

pub struct Circle {
    pub radius: f64,
}

impl Shape for Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * self.radius * self.radius
    }
}

pub struct Rectangle {
    pub width: f64,
    pub height: f64,
}

impl Shape for Rectangle {
    fn area(&self) -> f64 {
        self.width * self.height
    }
}
```

In order to use the trait methods defined on a type, we need to bring both the
trait and the type into scope.

We can only implement a trait on a type if at least one of the trait or the type
is local to our crate. We can't implement external traits on external types. 
This ensures that other people's code can't break your code and vice versa. Two
crates could implement the same trait for the same type, and Rust wouldn't know
which implementation to use.
