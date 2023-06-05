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
pub trait Shape { 
    fn area(&self) -> f64;
    fn perimeter(&self) -> f64;
}

pub struct Circle {
    pub radius: f64,
}

impl Shape for Circle {
    fn area(&self) -> f64 {
        std::f64::consts::PI * self.radius * self.radius
    }
    fn perimeter(&self) -> f64 {
        std::f64::consts::PI * self.radius * 2.0
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

    fn perimeter(&self) -> f64 {
        2.0 * (self.width + self.height)
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

## Default Implementations

You can implement a default implementation in the body of a trait method to
provide placeholder behavior of the method for types that do not have an
implementation in the type.


## Traits as Parameters

We can define a function that takes a type that implements some trait, e.g.:

```rust
pub fn summarize(s: &impl Shape) {
    println!("perimeter: {}; area: {}" s.perimeter(), s.area());
}
let c = Circle { radius: 1.0 };
summarize(&c);  //>>> perimeter: 6.283185307179586; area: 3.141592653589793

let r = Rectangle { width: 2.0, height: 4.5 };
summarize(&r);  //>>> perimeter: 13; area: 9
```

### Trait Bound Syntax

This is the syntax for which the `impl Trait` syntax above is syntactic sugar.
If we want to, e.g. ensure that two arguments have the same type that implements
some trait, we will define it like this:

```rust
pub fn function_name<T: Trait>(arg1: &T, arg2: &T) {}
```

We can specify more than one trait bound, e.g.

```rust
pub fn function_name(arg: &(impl Summary + Display)) {}
pub fn function_name<T: Trait1 + Trait2>(arg: &T) {}
```

### Clearer Trait Bounds with where Clauses

We can use a `where` clause after the function signature to make the function
definition easier to read.

```rust
// instead of
fn some_function<T: Display + Clone, U: Clone + Debug>(t: &T, u: &U) -> i32 {}

// we can write this in a less cluttered form like so:
fn some_function<T, U>(t: &T, u: &U) -> i32
where
    T: Display + Clone,
    U: Clone + Debug,
{}
```

## Returning Types that Implement Traits

You can also use the `impl Trait` syntax in the return position to return a
value of some type that implements a trait. However, you can only use
`impl Trait` if you’re returning a single type.
