# Rust Data Types

## Scalar Types

Represent a single value.

### Integers

These are whole numbers. Rust has the following built-in integer data types:

* **8-bit numbers**

| Data Type | Min Value | Max Value |
|-----------|-----------|-----------|
| `i8`      |      -128 |       127 |
| `u8`      |         0 |       255 |

* **16-bit numbers**

| Data Type | Min Value | Max Value |
|-----------|-----------|-----------|
| `i16`     |   -32,768 |    32,767 |
| `u16`     |         0 |    65,535 |

* **32-bit numbers**

| Data Type |          Min Value         |           Max Value           |
|-----------|----------------------------|-------------------------------|
| `i32`     | -2,147,483,648 ($-2^{31}$) |    2,147,483,647 ($2^{31}-1$) |
| `u32`     |                          0 |    4,294,967,295 ($2^{32}-1$) |

`i32` is the default integer size in Rust.

* **64-bit numbers**

| Data Type |  Min Value  |  Max Value  |
|-----------|-------------|-------------|
| `i64`     |   $-2^{63}$ |  $2^{63}-1$ |
| `u64`     |           0 |  $2^{64}-1$ |

* **128-bit numbers**

| Data Type | Min Value |  Max Value  |
|-----------|-----------|-------------|
|    `i128` |$-2^{127}$ | $2^{127}-1$ |
|    `u128` |         0 | $2^{128}-1$ |

* **Architecture dependent numbers**

| Data Type |  Min Value  |   Max Value   |
|-----------|-------------|---------------|
|   `isize` |  $-2^(w-1)$ | $2^{(w-1)}-1$ |
|   `usize` |           0 |     $2^{w}-1$ |

Here, *w* will be the word size of the platform in bits. For example, 64 on
64-bit machines. These are usually used when indexing some sort of collection.

### Signed Integers

Signed numbers are stored using *two's complement* representation. This is where
a number's most significant bit (called the *sign bit*) has negative sign. It 
has weight $-1*2^{(w - 1)}$ where *w* is the size of the number in bits.

For an 8-bit number, -128 as bits would be:

```
| 1 | 0 | 0 | 0 | 0 | 0 | 0 | 0 |
```

-1 would be

```
| 1 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
```

and 127 would be

```
| 0 | 1 | 1 | 1 | 1 | 1 | 1 | 1 |
```

### Integer Overflow

If an operation whose result is to be stored in an integer results in a value
greater than the type's maximum size, we say that *integer overflow* occured.
If we are compiling in debug mode, Rust includes checks for integer overflow
that cause the program to panic at runtime, but when compiling in release mode,
Rust performs *two's complement wrapping*, where the values wrap around to the 
minimum value that the type can hold. The program will not panic, but the 
variable will have a different value than expected.

Rust provides ***wrapping***, ***checked***, ***overflowing***, and 
***saturating*** methods to explicitly handle the possibility of overflow.

### Floating-Point Numbers

These are numbers with decimal points. Rusts's floating-point types are `f32`
and `f64`, which are 32 and 64 bits in size respectively. `f64` is the default
floating point type. All floating point types are signed.

Floating point numbers are represented according to the IEEE-754 standard. The
`f32` type is a single-precision float, and `f64` has double precision.

### Booleans

The Boolean type has two possible values: `true` or `false`. They are one byte
in size.

```rust
let f: bool = false;
```

### Characters

The `char` type is the language's most primitive alphabetic type. Rust `char`
types are four bytes in size and represent a Unicode Scalar Value.

```rust
let c = 'z';
let z: char = 'ℤ'; // with explicit type annotation
let heart_eyed_cat = '😻';
```

## Compound Types

These group multiple values into one type.

### Tuples

A tuple groups together several values with a variety of types into one compound
type.

```rust
let tup: (i32, f64, u8) = (500, 6.4, 1);
let (x, y, z) = tup;  // Use pattern matching to destructure a tuple value.
let first = tup.0;  // Accessing a tuple element directly
```

A tuple without any elements goes by ***unit***. It's value and type are written
`()`. It represents an empty value or an empty return type.


## Array

An array is a collection of multiple values that all have the same type.

```rust
let a: [i32; 5] = [1, 2, 3, 4, 5];

let a = [3; 5]  // Repeats the value 3 5 times.
```
