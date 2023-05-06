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

| Data Type |        Min Value       |         Max Value         |
|-----------|------------------------|---------------------------|
| `i32`     | -2,147,483,648 (-2^31) |    2,147,483,647 (2^31-1) |
| `u32`     |                      0 |    4,294,967,295 (2^32-1) |

* **64-bit numbers**

| Data Type | Min Value | Max Value |
|-----------|-----------|-----------|
| `i64`     |     -2^63 |    2^63-1 |
| `u64`     |         0 |    2^64-1 |

* **128-bit numbers**

| Data Type | Min Value | Max Value |
|-----------|-----------|-----------|
|    `i128` |    -2^127 |   2^127-1 |
|    `u128` |         0 |   2^128-1 |

Signed numbers are stored using *two's complement* representation. This is where
a number's most significant bit (called the *sign bit*) has negative sign. It 
has weight $-1 * 2 ^ (w - 1)$ where *w* is the size of the number in bits.

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

### Floating-Point Numbers

### Booleans

### Characters
