# Structs

A `struct` type is a heterogeneous collection of other types, called the fields
of the type, that are handled as a unit. A struct can have methods associated
with it that operate on its components. Rust has three kinds of struct types:
*named-field structs*, *tuple structs*, and *unit structs*.

A named-field struct gives a name to each field of a struct:

```rust
// defining
[#derive(Debug)] // Allows you to do `println!("{:?}", some_struct_name)`
struct StructName {
    field_1: int,
    field_2: String,
    field_3: SomeComplexType,
}
```

Instantiating:

```rust
let x = StructName {  // let mut x = ... for mutable struct.
    field_1: 1,
    field_2: String::from("some string value"),
    field_3: other_thing,
}

// Field init shorthand syntax
fn build_struct(field_1: int, field_2: String) -> StructName {
    StructName {
        field_1, // Because variables field_1 and field_2 have same name as the 
        		 // struct field names, we don't have to do field_name: field_value
        field_2,
        field_3: other_thing_2,
    }
}

// Struct update syntax (use values in a struct to populate another)
let z = StructName {
    field_1: 3,
    ..x  // Other fields have the same value as fields in instance `x`
}
```

Tuple structs identify fields by the order in which they appear:

```rust
// Defined just like tuples, but must include struct name.
struct Color(i32, i32, i32);  // Color and Point are different types
struct Point(i32, i32, i32);

let red = Color(255, 0, 0);
let origin = Point(0, 0, 0);

x = origin.0  // struct_var.index to access individual property
```

Named-field structs are usually more legible and easier for the user; we will
usually use tuple structs when we usually use pattern matching to find the
struct elements.

Unit structs do not have any fields at all:

```rust
struct StructName;
```

Memory layout of a struct is undefined by default to alow for compiler
optimizations, but may be fixed with the `repr` attribute. Fields may be given
in any order in a corresponding struct expression; the resulting `struct` value
will always have the same memory layout.

## Struct Methods

We can use `impl` block to define struct methods on any struct type we define.
In a type's `impl` block, `Self` is an alias for the type. Use `.` to refer to
associated functions that have `self` as their first parameter, and `::` to
refer to those that don't. These latter type of methods are functions associated
with the type itself, also referred to as *static methods*. An example is the
`Vec::new()` function for vectors that provides a constructor function.

```rust
#[derive(Debug)]
struct Rectangle {
	width: u32,
	height: u32,
}

impl Rectangle {
	fn area(&self) -> u32 {  // borrow (method can take ownership, which may not be ideal)
		self.width * self.height
	}

	fn set_width(&mut self, width: u32) {
		self.width = width;
	}
}
```

A struct can have many separate `impl` blocks for a single type, but they must
all be in the same crate that defines that type.