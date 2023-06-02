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
