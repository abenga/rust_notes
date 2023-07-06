# Vectors (`Vec`)

Is a sequence that stores a variable number of values next to each other. A
vector to store elements of type `T` has the type `Vec<T>`. A `Vec<T>` consists
of a pointer to the buffer allocated to hold the elements on the heap, the
number of elements that the buffer can store (its *capacity*, accessed using the
method `.capacity()`), and the number it contains now (its *length*, accessed
through the method `.len()`).

```rust
let v1: Vec<i32> = Vec::new(); // Creates a new empty vector to store integers.
let v2 = vec![1, 2, 3, 4, 5]; // Creates a vector with these values.
let eighth = v2[7]; // Out of bounds, will panic at run time.
let v3: Vec<i32> = (0..5).collect();  // build a vector from values produced by
                                      // an iterator. Usually needs a type
                                      // annotation because collect can build
                                      // different sorts of collections.

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

You can use the `.pop()` method to remove the last element and return it.
Popping a value from a `Vec<T>` returns an `Option<T>`, with `None` if the
vector was already empty, or `Some(v)` if the last element was `v`.

You can insert and remove elements wherever you like in a vector, although it
may be slow if we have many elements after the insertion point.

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

We can use slice methods on vectors, e.g. `reverse`, `sort`, etc.
