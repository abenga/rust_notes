# Error Handling

Rust groups errors into two categories: recoverable errors and unrecoverable 
errors.


## Unrecoverable Errors and `panic!`

When errors that cannot be recovered from occur, code usually calls the `panic!`
macro that causes the program to terminate immediately and provide feedback to 
the caller.

When a panic occurs, the program starts *unwinding*, and Rust walks back up the
stack and cleans up the data from each function it encounters. This is not 
trivial, however, and Rust provides the alternative of immediately *aborting*,
which ends the program without cleaning up. The host OS will then need to clean
up the memory that was in use by the program.

## Recoverable Errors with `Result`

Most errors are forseeable and recoverable.

A function that  could get recoverable errors  usually returns the type
`Result<T, E>`. This is defined in the module
[`std::result`](https://doc.rust-lang.org/std/result/) and is brought into 
scope by the prelude forall Rust programs. `Result` is an enum with the 
variants  `OK(T)` representing success and containing a value, and `Err(E)`
representing the error and containing an error value. `T` and `E` are generic
type parameters. `T` is type of the value to return in the success case, and 
`E` is the type of the error that will be returned in the error case.

```rust
enum Result<T, E> {  
    Ok(T),
    Err(E),
}
```

Using a library function that returns a result:

```rust
use std::fs::File;


let file_open_result_1 = File::open("some_nonexistent_file.txt");
println!("{:?}", file_open_result_1);
//>>> Err(Os { code: 2, kind: NotFound, message: "No such file or directory" })

// Using a function that returns a result.
let file_open_result_2 = File::open("file_name.txt");
let opened_file = match file_open_result_2 {
    // instance of `Ok` that contains a file handle.
    Ok(file) => file,

    // error is instance of `Err` that contains more info about failure.
    Err(error) => match error.kind() {
        // we can match on different errors
        ErrorKind::NotFound => match File::create("file_name.txt") {
            Ok(fc) => fc,
            Err(e) => panic!("Problem creating the file: {e:?}"),
        }
        other_error => {
            panic!("Problem opening the file: {:?}", other_error);
        }
    },
};
```

Writing a function that returns a result:

```rust
#[derive(Debug)]
enum Version { Version1, Version2 }

fn parse_version(header: &[u8]) -> Result<Version, &'static str> {
    match header.get(0) {
        None => Err("Invalid header length"),
        Some(&1) => Ok(Version::Version1),
        Some(&2) => Ok(Version::Version2),
        Some(_) => Err("invalid version"),
    }
}

let result_1 = parse_version(&[1, 2, 3, 4]);
match result_1 {
    Ok(v) => println!("working with version {v:?}"),
    Err(e) => println!("error parsing header: {e:?}"),
};
//>>> working with version Version1

let result_2 = parse_version(&[]);
match result_2 {
    Ok(v) => println!("working with version {v:?}"),
    Err(e) => println!("error parsing header: {e:?}"),
};
//>>> error parsing header: "Invalid header length"

let result_3 = parse_version(&[4, 5, 6]);
match result_2 {
    Ok(v) => println!("working with version {v:?}"),
    Err(e) => println!("error parsing header: {e:?}"),
};
//>>> error parsing header: "invalid version"
```

## Shortcuts for Panic on Error: `unwrap` and `expect`

`unwrap` is a shortcut method implemented to return the value inside the `Ok`
variant. If the result is the `Err` variant, `unwrap` will call the `panic!` 
macro.

```rust
let greeting_file = File::open("hello.txt").unwrap();
//>>> 
// thread 'main' panicked at 'called `Result::unwrap()` on an `Err` value: 
// Os { code: 2, kind: NotFound, message: "No such file or directory" }', 
// src/main.rs:21:49
//note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

`expect` lets us also choose the `panic!` error message.

```rust
let greeting_file = File::open("hello.txt")
        .expect("hello.txt should be included in this project");
//>>>
// thread 'main' panicked at 'hello.txt should be included in this project: 
// Os { code: 2, kind: NotFound, message: "No such file or directory" }', 
// src/main.rs:17:10
//note: run with `RUST_BACKTRACE=1` environment variable to display a backtrace
```

In production-quality code, it is conventional to use `expect` rather than 
`unwrap` and give more context about why the operation is expected to always
succeed.

## Propagating Errors

When a function's implementation call something that might fail, instead of
handling the error within the function itself, you can return the error to the
calling code to decide what to do. This is known as *propagating* the error.

```rust
use std::fs::File;
use std::io::{self, Read};

fn read_username_from_file() -> Result<String, io::Error> {
    let username_file_result = File::open("hello.txt");

    let mut username_file = match username_file_result {
        Ok(file) => file,
        Err(e) => return Err(e),
    }

    let mut username = String::new();

    match username_file.read_to_string(&mut username) {
        Ok(_) => Ok(username),
        Err(e) => Err(e),
    }
}
```

The code that calls this `read_username_from_file` will then handle getting an
`Ok` value that contains the username or an `Err` value that contains an 
`io:Error`.

We can use the `?` operator as a shortcut to hide some of the boilerplate 
involved in propagating errors up the call stack. The function thus becomes:

```rust
use std::fs::File;
use std::io;
use std::io::Read;

fn read_username_from_file() -> Result<String, io::Error> {
    let mut username_file = File::open("hello.txt")?;
    let mut username = String::new();
    username_file.read_to_string(&mut username)?;
    Ok(username)
}
```

The `?` placed after a `Result` value has the following meaning:

*   If the value is an `Ok`, the unwrappedvalue will be evaluated as the result
    of the expression, and the program will continue.

*   If the value is an `Err`, the `Err` will be returned early from the 
    enclosing function as if we had used the `return` keyword, so the value 
    gets propagated into the calling code. Error values returned in this way 
    go through the `from` function and are converted into the error type 
    defined in the return type of the current function.

    We can shorten the program even further by chaining the `?` calls:

    ```rust
    let mut username = String::new();
    File::open("hello.txt")?.read_to_string(&mut username)?;
    Ok(username)
    ```

    The `?` operator can only be used in functions whose return type is 
    compatible with the value the `?` is used on. It can also be used when we 
    want to return an `Option<T>` value, in which case it returns `None` in the
    early return, not an `Err`.

## When to Panic

It is advisable to have code panic when it is possible that the program could
end up in a bad state, i.e. some assumption, guarantee, contract, or invariant
is broken, and:

*   the bad state is unexpected, 
*   the code after the point relies on not being in the bad state,
*   or there isn't a good way to encode this information in the types we use.
