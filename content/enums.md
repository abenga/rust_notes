# Enumerations

Allow us to define a type by enumerating its possible *variants*. An instance of
the type can be one of the enumerated variants. For example, to represent an IP
address, we can define the following enumeration:

```rust
enum IPAddr {
    V4(u8, u8, u8, u8),
    V6(String),
}
let dns_addr_ipv4 = IpAddr::V4(8, 8, 8, 8);
let dns_addr_ipv6 = IpAddr::V6(String::from("2001:4860:4860::8888"));
```

Any `enum` value uses as much memory as the largest variant for its
corresponding `enum` type, as well as the size needed to store a discriminant.

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
