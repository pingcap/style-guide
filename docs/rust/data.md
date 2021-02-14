# Data structures

For generic data types, prefer to use the most flexible bounds. That usually means putting strict bounds only on `impl`s, not on the concrete datatype, and may mean splitting `impl`s into smaller blocks.
The generic parameters on the concrete type should only have bounds if they are intrinsic to the datatype itself (which includes lifetime bounds, `?Sized`, where the bound is required to name an associated type, or the bound is required by a `Drop` `impl`).
Generic parameters in concrete datatypes should never have derivable bounds.

Rationale: makes datatypes most flexible, reduces code duplication, and makes types more backwards compatible.
See [this section](https://github.com/rust-lang/api-guidelines/blob/master/src/future-proofing.md#data-structures-do-not-duplicate-derived-trait-bounds-c-struct-bounds) of the API guide for more details.

Avoid default generics, unless the default is used in the vast majority of situations (e.g., the default is only overridden for testing, or in very exceptional cases, such as supplying a hasher to a hashtable).
Rationale: having an explicit type often improves comprehensability of code and outweighs the convenience of skipping a generic parameter).

Use types to enforce invariants.
Take advantage of structs, tuples, and enums to define the valid states of an object.
Use associated types to enforce relationships between types.
Where possible, make invalid objects impossible.

Use type aliases (`type`) to provide convenience versions of common generic types (e.g., `type Result<T> = Result<T, MyErr>;`).


## Structs

* Prefer small structs.
  Build several small structs into one larger one.
  Rationale:
  - more borrow checker-friendly, see also [this design pattern](https://github.com/rust-unofficial/patterns/blob/master/patterns/compose-structs.md),
  - more flexible,
  - can lead to better design.
* Consider carefully whether fields should be public:
  - public fields are part of a struct's API,
  - prefer to use public fields to getter and setter methods,
  - if there are object invariants that include multiple fields, then all those fields must be private,
  - changing a public field in any way is a breaking change,
  - if all fields in a struct are public, then *adding* a field is a [breaking change](https://github.com/rust-unofficial/patterns/blob/master/idioms/priv-extend.md).
* Empty structs should be defined as `struct Foo;`, not `struct Foo {}` or `struct Foo();` (the latter two should only be used if necessary in macros).
* If a struct is `repr(C)` then the order of the fields is significant.
  The usual motivation for `repr(C)` objects is mapping them to objects in other languages.
  If the order is not constrained, then the most efficient representation is usually to list the fields in descending order of size.
* Restrict use of tuple structs to cases where there is only one field and it is obvious what that field is (usually because the struct is just a wrapper).
* Use the [newtype pattern](https://github.com/rust-unofficial/patterns/blob/master/patterns/newtype.md) where structs have the same data but different behaviour.
  

## Enums

* Enums with many variants are fine.
* Be aware that the size of an enum is the size of it's largest variant.
  Enums with very different sized variants are likely to be inefficient.
* Variants should not have more than about three fields.
* For more complex variants, the variant should have a single struct field with the complex fields moved to the struct.
  In this idiom, the struct should have the same name as the variant.
* Avoid named fields in variants.
  - Rationale: in most cases where a struct variant is better than a tuple variant, using a named struct is even better.
* Avoid empty variants, i.e., prefer `Foo` to `Foo()` or `Foo {}`.
* If an enum has an intrinsic null value, prefer to have a `None` variant, rather than wrapping it with an `Option`.
  - Similarly for `Result` and an `Err` variant.
  - Rationale: having a `None` variant in `Foo` has a different meaning to using `Option<Foo>` (may always be null vs may sometimes be null).
  - Using a `None` variant makes for simpler pattern matching and simpler types than using an `Option`.


## Tuples

* Use a tuple to represent n logically-separate pieces of data.
  Prefer using structs to tuples to represent a logical unit of data.
  It can be convenient to use tuples for 'temporary' data, e.g., returning two items from a function where the tuple should be destructured immediately.
* Only use tuples where the meaning is clearly multiple types, not as a convenience where a named type could be used.
  E.g., Use `Point { x: i32, y: i32 }` rather than `(i32, i32)`.
* A tuple with multiple elements of the same type is very rarely a good idea.

Rationale: named types and fields nearly always improve the readability of code.


## Unions

Only use unions for FFI to mirror a C enum. In Rust code prefer an enum.


## Builder pattern

A common way to construct concrete data with complex internals is the [builder pattern](https://doc.rust-lang.org/1.0.0/style/ownership/builders.html).
It is recommended to use the builder pattern to avoid a large number of constructor functions (or complex constructor functions with many `Option`s), but avoid using them instead of one or two simple constructor functions.

<p align="center">
<a href="modules.html">&lt;&lt; Modules and crates</a> | <a href="traits.html">Implementing traits &gt;&gt;</a>
</p>
