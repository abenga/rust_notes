# Treating Smart Pointers Like Regular References with the `Deref` Trait

Implementing the `Deref` trait allows you to customize the behavior of the
*dereference operator* `*`. By implementing `Deref` in such a way that a smart
pointer can be treated like a regular reference, you can write code that
operates on references and use that code with smart pointers too.
