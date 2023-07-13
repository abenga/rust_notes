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

Enumerations can have methods like structs. We just have to add an `impl` block.

## Enums without Data

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
enum) is not allowed, as an arbitrary integer could be missing from the enum.

## Enums with Data

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

`Option` is a predefined generic enum in the Rust standard library. It can take
one of two values - `Some(data)` or `None`.

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
    Quarter(USState),  // Tuple variant
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

### Patterns Used in `match`

A `match` expression takes the form

```rust
match some_val {
    pattern_1 => {
        // code to run if pattern_1 matches
    },
    pattern_2 => {
        // code to run if pattern_2 matches
    },
    // ... etc.
}
```

If a pattern contains simple variables, these become local variables in the code
follows the pattern.

The patterns can be:

* ***Literals*** - the pattern will match the value of the literal.

* ***Range*** - The pattern will match any value in the range. Is inclusive of
  the boundaries. (e.g. `0 ... 100`).

* ***Wildcard*** - Single underscore (`_`) matches any value and ignores it.
  This is usually used as the final "catch-all" branch of a match expression.

* ***Variable*** - (immutable or mutable) Moves or copies the value into a new
  variable.

* ***`ref` Variable*** - Borrows a reference to the matched value instead of
  moving or copying it.

* ***Binding with subpattern*** - Matches the pattern to the right of `@` using
  the variable name to the left. For example `val @ 0 ... 99` will match any
  values between 0 and 99, assigning them to the variable `val`.

* ***Enum pattern*** - Matches an enum value. `Some(value)` will also assign the
  contained value to the binding `value`.

* ***Tuple pattern*** - e.g. `(x, y, z)` will match a tuple value and assign the
  contents to the variables `x`, `y`, and `z`. Allows us to get multiple pieces
  of data in a single match.

* ***Struct pattern*** - Matches a struct value and stores the struct's fields
  in local variables. e.g. `Point {x, y, z}` will match any `Point` value and
  store its fields in the local variables `x`, `y`, and `z`. If you only care
  about the value of a subset of fields, you would do `Person { name, age, .. }`
  for example when you want to match on the `Person` struct, but only lift the
  person's `name` and `age` fields.

* ***Reference patterns*** - `ref` patterns borrow parts of a matched value,
  e.g. in the previous example, in

    ```rust
    match person {
        Person { name, age } => {
            // do some stuff with name and age
        }
    }
    // cannot use `person` here as the rest of `person` is dropped.
    ```

  `Person { name, age, .. }`, you would not
  be able to use the struct value after that, since the rest of the person
  struct would be dropped. A pattern that borrows matched values would be

    ```rust
    match person {
        Person { ref name, ref age, ..} => {
            // do some stuff with name and age
        }
    }
    // person remains usable here
    ```
  In this case name and language would be references to the corresponding fields
  in `person`, and `person` would remain perfectly usable after the `match`
  expression.

  You can also use `ref mut` to borrow mutable references.

  We can also use an `&` pattern to match a reference. For example:

    ```rust
    match person.contact_details() {  // returns an address of a private field
        &EmailContact { personal_email, work_email } => {},
        &PhoneContact { home_phone_number, mobile_phone_number } => {},
    }
    ```

* ***Multiple patterns*** - A vertical bar (`|`) can be used to combine several
  patterns in a single match arm.

* ***Guard expression*** - We can use the `if` keyword to add a *guard* to a
  match arm. The match succeeds only if the guard evaluates to `true`. For
  example `Some(val) if val % 2 == 0` would only match if `val` is even. 

* **`@` patterns** - `x @ pattern` matches the given `pattern`, but on success
  of creating variables for parts of the matched value, it creates a single
  variable `x` and moves or copies the whole value into it.

    ```rust
    match value {
        sq @ Shape::Square(..) => { // do some stuff with sq },
    }
    ```

Patterns can also be used in `let` expressions, to unpack a composite function
argument, iterate over a list of composite functions, or to automatically
dereference an argument to a closure.

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
