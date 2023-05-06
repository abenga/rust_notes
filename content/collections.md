# Common Collections

Collections are data structures that can contain multiple values. Unlike the
array and tuple types, data collections point to is stored on the heap, which
means that they can grow or shrink as the program runs.

The most commonly used collections are `String` type (a UTF-8 encoded, growable
string), `Vec` (a contiguous growable array type) and `Hashmap` (allows us to
associate arbitrary keys with an arbitrary value).

## Vectors (`Vec`)

Is a sequence that stores a variable number of values next to each other. A
vector to store elements of type `T` has the type `Vec<T>`.

```rust
let v1: Vec<i32> = Vec::new(); // Creates a new empty vector to store integers.
let v2 = vec![1, 2, 3, 4, 5]; // Creates a vector with these values.
let eighth = v2[7]; // Out of bounds, will panic at run time.

let v2_third: &i32 = &v2[2];  // Read the third element using indexing

// Get an option we can use with match.
let v2_fourth: Option<&i32> = v.get(2);

let v2_seventh = v.get(7);  // Outside vector bounds - Will return None

// Create a vector then add elements to it.
let mut v = Vec::new();
v.push(5);
v.push(6);
v.push(7);
v.push(8);
```

We can use a `for` loop to access each element in a vector in turn. In the `for`
loop, we get references to each element.

```rust
let v = vec![100, 32, 57];
for n_ref in &v {
    let n_plus_one: i32 = *n_ref + 1;
    println!("{}", n_plus_one);
}

// Incrementing the contents of v in place
let mut v = vec![2, 3, 4, 5];
for n_ref in &mut v {
    *n_ref += 50
}
```

## Strings (`String`)

Collection of characters.


## Hash Map (`HashMap`)

Allows us to associate a value with a particular key.
