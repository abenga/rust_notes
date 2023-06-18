# Smart Pointers

A *pointer* is a general concept for a variable that contains an address in
memory, i.e. it refers to some other data. The most common kind of pointer in
Rust is a reference, which borrows the value they point to.

*Smart pointers* are data stractures that act like a pointer but also have
additional metadata and capabilities. While references only borrow data, in many
cases, smart pointers own the data they point to.

Smart pointers are usually implemented using structs that, unlike ordinary
structs, implement the `Deref` (allows an instance of the smart pointer to
behave like a reference) and `Drop` (allows us to customize the code that is
run when an instance of the smart pointer goes out of scope) traits.

Examples of smart pointers:

*   `String`
*   `Vec<T>`
*   `Box<T>` - Allocates values on the heap
*   `Rc<T>` - Enables multiple ownership
*   `Ref<T>` and `RefMut<T>`, accessed through `RefCell<T>`, a type that
    enforces the borrowing rules at runtime instead of compile time.
    