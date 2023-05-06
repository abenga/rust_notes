# Structs

Defining:

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

Tuple structs:

```rust
struct Color(i32, i32, i32);  // Color and Point are different types
struct Point(i32, i32, i32);

let red = Color(255, 0, 0);
let origin = Point(0, 0, 0);

x = origin.0  // struct_var.index to access individual property
```

## Struct Methods

Use `impl` block to define struct methods. In a type's `impl` block, `Self` is
an alias for the type. Use `.` to erfer to associated functions that have `self`
as their first parameter, and `::` to refer to those that don't.

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