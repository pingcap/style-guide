# Implementing traits

TODO

- iterators
- derive
- operator overloading
- index
- deref

* Prefer implementing a trait to adding an inherent method where a trait already exists (e.g., prefer to implement `Default` rather than have a `new` function with no arguments).

TODO ordering of impls

* Follow the rules of traits
  - hash and eq
  - borrow and deref
  - as_ref (or borrow) and eq, etc

* Unsafe impls can introduce global unsafety, be very careful!

* Implement `Debug` wherever possible.
