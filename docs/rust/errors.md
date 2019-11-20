# Error handling

* Remember that errors are part of your API.
* Follow error handling in existing code - don't introduce new libraries or techniques unless you will convert a whole crate.
* Don't make your own `Result` type (aliasing is ok).
* Don't use `Option` to signal an error, an `Option` is used where a value may or may not exist, but either state is correct.
* Use `?`, not `try!` (rationale: ecosystem convention to use modern Rust idioms).
* Prefer to use `?` or `Result` methods rather than `match` (rationale: more concise and readable code).
* Error types should compose: it should be possible to nest and abstract error types.
* Error types should implement `Debug` and `Display` (rationale: makes debugging easier).
* Error types should implement `std::error::Error` (rationale: interoperability).
* Error types should implement `Sync` and `Send` (rationale: it is common to pass errors across threads).
  - if you use a trait object, use the bounds `Error + Send + Sync + 'static`.
* Prefer to keep context in an error (e.g., by chaining error causes).
* Prefer structured data to stringified data in errors.
  - rationale: better data means we can make better error messages with more information for debugging.


## Panicking

* Prefer to return a `Result` rather than panicking: this gives the caller the option of panicking if they want to.
  - Prefer using `?` to `unwrap`.
  - Errors can be 'bubbled up' all the way to the client if we can't recover.
  - Exception: in code where we can recover from a crashed process, it is fine to panic if we cannot recover.
* Prefer `expect` with a short message to `unwrap`.
  - exception - when unlocking a mutex or similar operation where failure is unlikely, recovery is likely impossible, and the reason for unwrapping is well-known.
* `unwrap` or `expect` should only be used where it is impossible to fail (e.g., after a result has already been checked with `is_ok`, although `if let` is usually a better solution here), or where it is impossible to recover from failure (e.g., mutex poisoning).
* `unwrap` (or `expect`) can be freely used in tests.
* If a function can panic, that should be conveyed in the name of the function - usually by including `unwrap` or `assert`.
* If a function can panic, that behaviour shoud be described in its documentation.
* Bear in mind that indexing an array can panic.


### Rationale

* Results are for recoverable errors, panicking is for unrecoverable errors.
  It is usually better to recover than to crash the process, but TiKV can recover from crashes.
* Make panics as easy as possible to debug.
* Existing guidelines: [API guide](https://github.com/rust-lang-nursery/api-guidelines/blob/master/src/interoperability.md#error-types-are-meaningful-and-well-behaved-c-good-err).
