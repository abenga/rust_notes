# Generic Types, Traits, and Lifetimes

*Generics* are abstract stand-ins for concrete types or other properties. In
*generic programming*  we write code (functions or data types) in terms of 
unspecified types that are instantiated later for specific (concrete) types that
are provided as parameters. This means a function/type can be written to use any
of a set of types.

## Generic Data Types

### In Function Definitions

We can use generics to create definitions for items like function signatures or
structs, which we can then use for many different data types. For example, we
can write a `largest` function that finds the largest element in a slice of
values of any type that implements a partial ordering relation between two
instances of itself.

```rust
// <T> is the type name declaration. The definition below says that the function 
// `largest` is generic over some type `T` that is restricted to the set of 
// types that have partial ordering implemented (by implementing the 
// std::cmp::partialOrd trait)
fn largest<T: std::cmp::PartialOrd>(list: &[T]) -> &T {
    let mut largest = &list[0];

    for item in list {
        if item > largest {
            largest = item;
        }
    }

    largest
}
```

### In Struct Definitions

Structs can also be defined to use generic type parameters. For example,

```rust
struct Point<T> {  // Point<T> is generic over some type T.
    x: T,  // Both x and y are both the same type.
    y: T,
}
let integer_point = Point { x: 5, y: 10 };
let float_point = Point { x: 1.2, y: 4.0 };

struct Point<T, U> {  // multiple type parameters
    x: T,  // x is of type T
    y: U,  // y is of type U
}
```

### In Enum Definitions

We can define enums to hold generic data types in their variants, e.g. the 
`Option<T>` and `Result<T, E>` enums.

```rust
enum Option<T> {  // generic over one type
    Some(T),
    None,
}

enum Result<T, E> {  // generic over two types
    Ok(T),
    Err(E),
}
```

### In Method Definitions

We can implement methods on structs and enums and use generic types in their
definitions.

```rust
struct Point<T> {
    x: T,
    y: T,
}

impl<T> Point<T> where &T: std::ops::Sub<&T> {
    fn x(&self) -> &T {
        &self.x
    }
}
let p = Point { x: 5, y: 10 };
println!("p.x = {}", p.x());  //>>> p.x = 5
```

## Performance of Code Using Generics

There is no performance penalty for usin gGenerics in code, since rust performs
monomorphization of the code at compile time, i.e. generates an instance of 
compiled code for each concrete type the generic code is called with.
