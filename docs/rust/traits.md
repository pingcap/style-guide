# Implementing traits

Write `impl`s in the following order:

* inherent `impl`s,
* `impl`s for important traits,
* `impl`s for marker traits (e.g., `Sync`, `Send`),
* `impl`s for utility traits (e.g., `Debug`, `Into`).

Group `impl`s for the same traits together.
Write `impl`s directly after their concrete types.
Write blanket `impl`s directly after their traits.

Prefer to write all `impl`s in a single module.
However, very large `impl`s may be split across modules if this improves readability.

There can be multiple `impl`s with the same signature in one module, if grouping methods in this way improves readability.
Doing this is useful because the grouping will be preserved by Rustdoc and any documentation on an `impl` is used as an 'introduction' to a group of methods.

In crates which are useful to others and published, types should implement as many of the common traits from std as possible (for maximum flexibility and interoperability).
In crates which are not published, implement traits as needed (YAGNI).

Prefer to use `derive` rather than a manual `impl` where possible.

Prefer implementing a trait to adding an inherent method where a trait already exists (e.g., prefer to implement `Default` rather than have a `new` function with no arguments).

Unsafe impls can introduce global unsafety, be very careful!


### Rationale

* Put code close to data.
* Put important code first.
* Have a fixed order to make things easier to find.
* Reduce boilerplate.
* Make data types most usable with generic code.


## Follow the documented rules (and guidelines) for traits

Some invariants involved multiple traits.
For example, if an object has an `impl` for `Hash`, then if two objects are equals (via the `Eq` trait) then they must have the same hash.

Many traits are markers of semantics beyond the types of their functions.
For example, `Debug` and `Display` have the same functions but are intended for different purposes.
Use the appropriate trait, don't use a trait beyond its intent.

Some traits are very similar, but their are recommended traits to use where possible.
For example, prefer to implement `From` rather than `Into` (due to a blanket `impl`).

Many traits have versions which may and may not fail.
For example, `From` and `TryFrom`.

Some more examples from the standard library:

* [`AsRef`](https://doc.rust-lang.org/nightly/std/convert/trait.AsRef.html) and [`Borrow`](https://doc.rust-lang.org/nightly/std/borrow/trait.Borrow.html) are very similar.
* `Borrow` has requirements on `Eq`, `Ord`, and `Hash`, `AsRef` is more flexible.
* If you implement `AsMut` you should probably also implement `AsRef`.
* `AsMut` and `AsRef` should be cheap.
* If you implement `Deref`, you should probably also implement `AsRef` or `Borrow`.
* If you implement `Borrow`, you should probably implement `ToOwned` (for the inverse conversion).

Marker traits such as `Sync` and `Unpin` usually have strong semantics that must be followed.


## Some common traits

Implement `Debug` wherever possible.
The output of `Debug` should be meaningful and useful, but should not be too long (since that can overwhelm logs).

Most collections should have an iterator which implements `Iterator` and implement `IntoIterator` themselves.
They may also provide an inherent `iter` method for return a borrowed iterator, see [`slice::iter`](https://doc.rust-lang.org/std/primitive.slice.html#method.iter) for an example.

Only implement operator traits where the operations are clearly and obviously those suggested by the operator.
For example, a vector type should not implement `Mul` because both the cross product and dot product might be considered a multiply operation, but may implement `Add` since vector addition is well-known, well-defined, and unambiguous.
If in doubt, only use implement operator types for numeric or logical types.

Only implement `Index` for collection types.
Most collections should implement `Index`, `IntoIterator`, and `FromIterator`.
Most mutable collections should implement `Extend`.

Only implement `Deref` for smart pointer types; never for general coercion.
Most smart pointers should implement `Deref` and `Borrow`.

`Drop` should be implemented for any types which need to tidy-up, manual destructors should never be used.
`Drop` implementations must *never* panic.
Be aware of implicit panics, for example due to bounds checking in indexing.
`Drop` implementations should not fail in other way.
`Drop` implementations should not block.

If a destructor might panic, fail, or block, provide a method which performs the shutdown behaviour and which returns a result (or, if necessary, panic), e.g., a `close` method.
The user may then call the 'shutdown' method and check for errors.
Then the destructor is guaranteed to be well-behaved.
This should be documented.

`Fn`, `FnMut`, and `FnOnce` should be implemented by function-like objects which can be called (e.g., callbacks).
Uses of these traits should be rare, usually closures or futures are a better choice than a custom callback.
Don't abuse these traits for custom call-like behaviour.

### Rationale

* Abide by conventions outlined in standard library docs.
* Keep code easy to read by avoiding overuse of operator overriding and special-purpose syntax.
* Avoid unexpected behaviour.
* Rust prefers to avoid implicit coercion.

<p align="center">
<a href="data.html">&lt;&lt; Data types</a> | <a href="functions.html">Functions, methods, and traits &gt;&gt;</a>
</p>
