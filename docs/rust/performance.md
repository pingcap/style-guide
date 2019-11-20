# Performance

* "Premature optimization is the root of all evil"
  - Optimisation should be justified by profiles and benchmarks.
  - Prefer to write simple, clear code unless optimisation is *necessary*.
* Don't write sloppy code:
  - Avoid *unnecessary* allocation and copying.
  - Use iterators.
  - Don't use `Arc` where `Rc` would work; don't use a `Mutex` where a `RefCell` would work.
  - Avoid global, mutable state.
  - Prefer to use `push` and `push_str` to build strings rather than the `format` macro.
  - Don't worry about empty Vecs/Strings - they don't allocate and are very cheap.
* Don't hide potentially expensive code
  - code that may do IO, block, or sleep should be clearly named and documented.
  - allocation should be either expected from the purpose of a function or be documented (e.g., conversion functions should not allocate).
* Consider the computational complexity of algorithms, but bear in mind the expected size of input.
* Consider the concurrency of code.


## Rationale

* Modern compilers and CPUs can optimize simple code more effectively than complicated code; modern CPUs are complex, it is very difficult to predict whether hand-optimised code is effective.
* Allocation is a primary cause of slowdown in many programs.
* Use iterators to avoid slow bounds checks.
* Don't surprise clients of your code.
* Performant code requires scaling to multiple threads, blocking threads on locks, IO, etc., kills parallelism.

<p align="center">
<a href="unsafe.md">&lt;&lt; Unsafe code</a> | <a href="README.md">^ Contents ^</a>
</p>
