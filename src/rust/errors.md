# Error handling

* Remember that errors are part of your API.
* Follow error handling in existing code - don't introduce new libraries or techniques unless you will convert a whole crate.
* Don't make your own `Result` type (aliasing is ok).
* Use `?`, not `try!` (rationale: ecosystem convention to use modern Rust idioms).
* Error types should compose: it should be possible to nest and abstract error types.
* Error types should implement `Debug` and `Display` (rationale: makes debugging easier).
* Error types should implement ` std::error::Error` (rationale: interoperability).
* Error types should implement `Sync` and `Send` (rationale: it is common to pass errors across threads).
* Prefer to use concrete error types rather than `Error` trait objects (rationale: simplicity).
  - if you must use a trait object, use `Error + Send + Sync + 'static` (rationale: it is common to pass errors across threads).


## Panicking

* Prefer to return a `Result` rather than panicking: this gives the caller the option of panicking if they want to.
  - Prefer using `?` to `unwrap`.
* Avoid `expect` with a short message to `unwrap`.
  - exception - when unlocking a mutex or similar operation where failure is unlikely, recovery is likely impossible, and the reason for unwrapping is well-known.
* `unwrap` or `expect` should only be used where it is impossible to fail (e.g., after a result has already been checked with `is_ok`, although `if let` is usually a better solution here), or where it is impossible to recover from failure (e.g., mutex poisoning).
* `unwrap` (or `expect`) can be freely used in tests.
* If a function can panic, that should be conveyed in the name of the function - usually by including `unwrap` or `assert`.
* If a function can panic, that behaviour shoud be described in its documentation.
* Bear in mind that indexing an array can panic.


### Rationale

* Panicking is dangerous and should never happen in production (unless it is caught and handled).
* Make panics as easy as possible to debug.
* Existing guidelines: [API guide](https://github.com/rust-lang-nursery/api-guidelines/blob/master/src/interoperability.md#error-types-are-meaningful-and-well-behaved-c-good-err).
