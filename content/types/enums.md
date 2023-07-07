# Enumerations

*Enumerations* (also called *enums*) allow us to define a type by enumerating
its possible *variants*. An instance of the type can be one of the enumerated
variants. Any `enum` value uses as much memory as the largest variant for its
corresponding `enum` type, as well as the size needed to store a discriminant
(integer locally associated to it that is used to determine which variant it
holds, usually an `isize` value).

An enumeration is a simultaneous definition of the enumerated type as well as
a type of *constructors* that can be used to create or pattern-match values of
the corresponding enumerated types.


We can have simple, C-style enums, for example

```rust
enum Animal {
    Dog,
    Cat,
}

let mut a: Animal = Animal::Dog;
```

declares a type `Animal` with two possible values, called *variants* of
*constructors*: `Animal::Dog` and `Animal::Cat`. In memory, values of enums are
stored as integers, starting at 0 by default. You can tell Rust which integers
to use, e.g. in

```rust
enum HttpStatus {
    Ok = 200,
    NotModified = 304,
    NotFound = 404,
    InternalServerError = 500,
    ...
}
```

You can cast from an enum to an integer, but the reverse cast (from integer to
enum) is not allowed, as an arbitrary integer could be a value not listed in the
enumeration.

Enumerations can have methods like structs. We just have to add an `impl` block.

`enum` constructors can have named or unnamed fields:

```rust
enum Animal {
    Dog(String, f64),  // tuple variant
    Cat { name: String, weight: f64 },  // struct variants
}

let d: Animal = Animal::Dog("Scooby Doo".to_string(), 75.0);
let c: Animal = Animal::Cat{ name: "Garfield".to_string(), weight: 5.0 };
```

## The `Option` Enum

`Option` is a predefined enum in the Rust standard library. It can take one of
two values - `Some(data)` or `None`.

```rust
enum Option<T> {
    Some(T),  // contains value to be returned, of type T.
    None      // return "nothing" (null in some other languages)
}
```

Using `Option`:

```rust
// Getting inner T value of Option:
fn increment(x: Option<i32>) -> Option<i32> {
    match x {
        Some(i) => Some(i + 1),
        None => None,
    }
}

let age = Some(32);
let age_next_birthday = increment(age);
let empty = increment(None);
```

## `match` Control Flow

`match` is a control flow construct that allows you to compare a value against
a series of patterns and execute code depending on which pattern matches.

```rust
#[derive(Debug)]
enum USState {
    Alabama,
    Alaska,
    // ... etc
}

enum Coin {
    Penny,
    Nickel,
    Dime,
    Quarter(USState),
}

fn value_in_cents(coin: Coin) -> u8 {
    match coin {
        Coin::Penny => 1,
        Coin::Nickel => 5,
        Coin::Dime => 10,
        Coin::Quarter(state) => {
            println!("State quarter from {:?}", state);
            25
        },
    }
}
```

The compiler enforces the constraint that a `match` arm's patterns covers all
possibilities. We can use a variable name as a catch-all value to cover "all
other possiblities" in a `match` statement if we want to use the value, or `_`
if we do no t need to use the catch-all value (we ignore all other values).

If an enum contains non-copyable data and we want to peek into an enum without
moving its contents, we should match on a reference.

```rust
let opt: Option<String> = Some(String::from("Hello world!"));

match &opt { // if we matched on `opt`, `opt` will lose ownership of String
    Some(s) => println!("{:?}", s), // `s` has type &String
    None => println!("Empty!"),
}
println!("{:?}", opt); // we can use `opt` after the match.
```

## `if let`

Lets us combine `if` and `let` into a less verbose way to handle values that
match one pattern while ignoring the rest.

instead of

```rust
let config_max = Some(3u8);
match config_max {
    Some(max) => println!("The maximum is configured to be {}", max),
    _ => println!("No maximum configured!"),
}
```

we can write

```rust
let config_max = Some(3u8);
if let Some(max) = config_max {
    println("The maximum is configured to be {}", max);
} else {
    println!("No maximum configured!");
}
```
