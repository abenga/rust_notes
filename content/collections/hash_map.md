# Hash Map (`HashMap`)

Allows us to associate a value with a particular key. A variable of type
`Hashmap<K, V>` stores a mapping of keys of type `K` to values of type `V` 
using a *hashing function* which determines how it places the keys and values
into memory.

Hash maps store their data on the heap. They are homogeneous -- all of the keys
must have the same type as each other, and all the values must have the same
type as well.

## Creating Hash Maps and Adding Items

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);
```

For items that implement the `Copy` trait, like `i32`, the values are copied 
into the hash map. For owned values like `String`, the values will be moved and
the hash map will be the owner of the values. We cannot use variables after they
have been moved into the hash map with a call to `insert`.

If you call `insert` and give a key that already exists, the key's value will 
be overwritten with the new value.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Blue"), 20);

println!("{:?}", scores) //>>> {"Blue": 20}
```

Sometimes we want to check whether a particular key exists in the hash map with
a value and if it exists in the hash map, the existing value should remain as 
is. If it does not exist, insert the new value.

We can use the `entry` method to do this. `entry` returns an enum called `Entry`
that represents a value that might or might not exist.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();
scores.insert(String::from("Blue"), 10);

scores.entry(String::from("Yellow")).or_insert(50);
scores.entry(String::from("Blue")).or_insert(50);  // Will not change "Blue" val

println!("{:?}", scores); //>>> {"Blue": 10, "Yellow": 50}
```

## Accessing Values

We can get a value out of a hash map by providing its key to the the `get` 
method.

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

let blue_team = String::from("Blue");
let score = scores.get(&blue_team);  //>>> Some(10) (is an Option<&i32>)

// To get a copied value (Option<i32>)
let score = scores.get(&blue_team).copied(); //>>> Some(10) (is an Option<i32>)

// To return a default value
let green_team = String::from("Green");
let green_score = scores.get(&green_team).copied().unwrap_or(0); //>>> 0
```

## Iterating

```rust
use std::collections::HashMap;

let mut scores = HashMap::new();

scores.insert(String::from("Blue"), 10);
scores.insert(String::from("Yellow"), 50);

for (key, value) in &scores {
    println!("{}: {}", key, value);
}
```

## Updating

```rust
 use std::collections::HashMap;

let mut scores = HashMap::new();

// Overwrite a value
scores.insert(String::from("Blue"), 10);
println!("{:?}", scores); //>>> "{"Blue": 10}"
scores.insert(String::from("Blue"), 25);
println!("{:?}", scores); //>>> "{"Blue": 25}"

// Add a key only if a Key isn't present
scores.entry(String::from("Yellow"), 20);
println!("{:?}", scores); //>>> "{"Blue": 25, "Yellow": 20}" (order may be different)
scores.entry(String::from("Blue"), 50);
println!("{:?}", scores); //>>> "{"Blue": 25, "Yellow": 20}" (nothing changes)

// Update value based on old value
let text = "hello world wonderful world";

let mut map = HashMap::new();

for word in text.split_whitespace() {
    let count = map.entry(word).or_insert(0);
    *count += 1;
}

println!("{:?}", map); //>>> {"hello": 1, "world": 2, "wonderful": 1} (order may be different)
```

## Hash maps and Ownership

Fro types that implement the `Copy` trait, like `i32`, the values are copied
into the hash map. For owned values like `String`, the values will be moved and
the hash map will be the owner of those values.

## Hashing Functions

By default, `HashMap` uses the [SipHash](https://en.wikipedia.org/wiki/SipHash)
hashing function that provides resistance to DoS attacks involving hash tables.
You can switch to another function with different tradeoffs by specifying a
different *hasher* (a type that implements the `BuildHasher` trait) with
different desired tradeoffs (speed, security).
