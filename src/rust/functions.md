# Functions, methods, and traits

## Functions

* Prefer short functions and methods.
* Don't use `#[inline]` (or `never` or `always`) unless benchmarking shows an improvement.

## Methods

* Where there is a clear receiver (`self`), prefer a method to a function.
* If a function is only used by methods of a single data type, it should be a static method of that data type.
* If a function has no clear receiver and creates a data type, it should be a static method of that data type.
  - All constructor functions should be static methods.
* Do not implement getters and setters unless necessary; prefer public fields.
* Prefer using a `derive` attribute to explicitly implementing a trait.

## Generics

* Consider the trade-off between generics and object types.
* For generics with a single, simple bound, prefer using `impl` syntax.
* For generics with complex bounds, prefer using a `where` clause.
* Use lifetime elision wherever possible (under 2018 rules).
* TODO function types
* Avoid default generics, unless the default is used in the vast majority of situations (e.g., the default is only overridden for testing, or in very exceptional cases, such as supplying a hasher to a hashtable).
  - Rationale: having an explicit type often improves comprehensability of code and outweighs the convenience of skipping a generic parameter).
* Use type aliases (`type`) to provide convenience versions of common generic types (e.g., `type Result<T> = Result<T, MyErr>;`).

TODO how many generic arguments


## Traits and impls

* Use traits for most aspects of a type's behaviour; reserve inherent methods for actions on the concrete data.

## Associated types

* assoc types vs generics
