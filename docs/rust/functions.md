# Functions, methods, and traits

* Conversions should use conversion traits where possible, rather than ad hoc methods.
  E.g., implement `From<T>` or `TryFrom<T>`, rather than have an inherent `from_T` or `into_T` method.
  - See also [the API guidelines](https://github.com/rust-lang/api-guidelines/blob/master/src/interoperability.md#conversions-use-the-standard-traits-from-asref-asmut-c-conv-traits).
* Where there is a conversion from `T` to `U` and where `T` has more information than `U`, put conversion methods on `T` and not `U`.
  Where there is a (lossy) conversion from `U` to `T`, put that on `T` too (presumably such a conversion might fail, or will need extra information, so you might not be able to use a standard trait).
  - Rationale: prevents having many conversions on the 'lowest information' types.
    Prevents code duplication.
    See also [the API guide](https://github.com/rust-lang/api-guidelines/blob/master/src/predictability.md#conversions-live-on-the-most-specific-type-involved-c-conv-specific).
* Where there is a clear receiver (`self`), prefer a method to a function.
  - Rationale: methods are usually easier to use than functions, see [the API guide](https://github.com/rust-lang/api-guidelines/blob/master/src/predictability.md#functions-with-a-clear-receiver-are-methods-c-method)

There is often a choice between using a type's concrete name and using `Self`, e.g., in function signatures or when qualifying names.
In general, use `Self` when the meaning is 'the current type', and the explicit type name when the meaning is 'that specific type'.
Some examples:

* Constructors should usually use the explicit type name.
* Calls to private static methods should usually use `Self`.
* Methods which expect an object of the same type, e.g., comparisons, should use `Self`.

If in doubt, use the explicit type name.


## Functions

* Prefer short functions and methods.
  - The shorter the better, but as an upper limit, you should nearly always be able to read a whole function without scrolling on a regular monitor.
  - Split a large function into several smaller ones.
  - If that is difficult, it is often possible to factor out a data structure and turn the large function into small methods.
* Don't use `#[inline]` (or `never` or `always`) unless benchmarking shows an improvement.
* When creating an object, a constructor with no arguments should be an implementation of `Default`.
  The most common constructor should be an inherent, static method called `new`.
  If there are one or two other constructors with clear roles, they should be inherent, static methods with a `with_` prefix.
  If there is more complexity, prefer to use the [builder pattern](https://github.com/rust-lang/api-guidelines/blob/master/src/type-safety.md#builders-enable-construction-of-complex-values-c-builder).
* Functions which take ownership of an argument, should take that argument by value or as a `Box`.
  They should not take a reference and clone.
  - Rationale: avoid unnecessary cloning.
* Functions should prefer to take a borrowed reference, unless they need to take ownership of some form.
* Functions should prefer to return an object by value, rather than using a `Box`, `Rc`, or other smart pointer.
  - Rationale: increases flexibility because the caller can decide whether to keep the object by value or pointer, without unnecessary allocation.
* Prefer to use generics and trait bounds (or `impl Tr`) rather than concrete types.
  However, if this flexibility is not necessary, prefer concrete types if generic types make the function overly complex.
  - Rationale: makes functions more flexible and easier to test.
* Arguments should prefer to take a custom enum which conveys meaning, rather than using a `bool` or `Option`.
  - Rationale: makes code more readable, future proof.
  - See also [the API guide](https://github.com/rust-lang/api-guidelines/blob/master/src/type-safety.md#arguments-convey-meaning-through-types-not-bool-or-option-c-custom-type).
* Prefer to use the type system to validate function arguments
  - If not possible, validate arguments dynamically.
  - See also [the API guide](https://github.com/rust-lang/api-guidelines/blob/master/src/dependability.md#functions-validate-their-arguments-c-validate).
* If using `Read` or `Write` bounds, take the argument by value rather than (mutable) reference.
  - See [API guide](https://github.com/rust-lang/api-guidelines/blob/master/src/interoperability.md#generic-readerwriter-functions-take-r-read-and-w-write-by-value-c-rw-value).


## Methods

* If a function is only used by methods of a single data type, it should be a static method of that data type.
* If a function has no clear receiver and creates a data type, it should be a static method of that data type.
  - All constructor functions should be static methods.
* Do not implement getters and setters unless necessary; prefer public fields.

## Generics

* Consider the trade-off between generics (or `impl Tr`) and object types (`dyn Tr`).
  Generics are usually, but not always, better.
* For generics with a single, simple bound, prefer using `impl` syntax.
* For generics with complex bounds, prefer using a `where` clause.
* Use lifetime elision wherever possible (using 2018 rules).
* Function types should always be in `where` clauses.
* Use the most flexible function type you can.
  I.e., prefer to use `Fn` over `FnMut` over `FnOnce` for argument types.
  - Aside, if you ever need to implement a function trait, prefer to implement `FnOnce` over `FnMut` over `Fn`.
* More than about three generic parameters are an indication that the function might be better factored as multiple functions or a data type, or that some generic parameters might be better as associated types.

### Flexible function arguments

For each of these flavours of flexible function arguments, there is a trade-off.
The generic form is more flexible.
However, the generic form makes the function signature more difficult to read, can cause code bloat, adds boilerplate to the signature and body, and in some rare cases generates less performant code.

Some things to consider:

* Is the function public API?
  If so, prefer the more flexible version, otherwise prefer the simple version.
* Are there places in existing code where the function is called with conversion at the callsite?
  If so, prefer the flexible version if it reduces code duplication.
* Is the simple type commonly found in different flavours?
  E.g., `str` has `OsStr` and `CStr` variations, and the owned version (`String`) is common; if you intend to iterate a collection, it is common to pass both the collection and an iterator.
  In these cases, prefer the flexible version.
* Otherwise, prefer the simple version (rationale: KISS, YAGNI).

Some examples of this theme:

* `impl AsRef<T>` is a more flexible alternative to `&T`.
* `impl Into<T>` is a more flexible alternative to `T`.
  - a special case of this is `IntoIterator`.
* `Cow` lets a function take either an owned or borrowed value.
  - A special case is `Cow<'static, T>`, which takes either an owned or static value, it is commonly used with `str` and/or in places which commonly take a `const` or `static` value, but rarely take a custom value (perhaps for testing).
  Using `Cow` here prevents an unnecessary allocation in the common case.


## Traits

* Use traits for most aspects of a type's behaviour; reserve inherent methods for actions on the concrete data.
* Prefer small traits which model a single aspect of behaviour.
  - Think of traits as aspects of behaviour, rather than collections of functionality.
* Use the extension trait pattern to offer core and extended behaviour.
  - Useful for extending traits in a downstream crate.
  - Useful for having a core trait which is object-safe and a larger trait with extended behaviour.
  - An extension trait to `Foo` should be called `FooExt` if it is always valid.
  - See [RFC 445](https://github.com/rust-lang/rfcs/blob/master/text/0445-extension-trait-conventions.md) for more details.


## Associated types

Generic type parameters and associated types are not the same.
Generic type parameters are chosen by the user of a trait.
Associated types are chosen by the implementer of a trait.
For more details see [the book](https://doc.rust-lang.org/1.30.0/book/2018-edition/ch19-03-advanced-traits.html).

It is often useful to supply a default type for associated types.
If you add an associated type to a trait, using a default type makes the change backwards compatible.
