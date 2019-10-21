# Unsafe code

* Avoid `unsafe` code where possible.
* If using `unsafe` for performance, there must be clear evidence from benchmarks that there is a significant improvement.
* Be very careful about undefined behaviour - `unsafe` code has stricter semantics than C.
* Prefer `MaybeInit` to any use of uninitialized memory.
  - Almost any use of uninitialized memory will lead to UB.
* Where possible encapsulate `unsafe` code inside a safe function which re-establishes invariants before returning.

See also the [nomicon](TODO).

## Documentation

* Document *every* use of `unsafe`.
  - Even trivial uses should have a comment stating why it is trivial.
  - Including `unsafe impl`.

## Unsafe boundaries
