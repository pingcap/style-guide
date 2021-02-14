# Performance

* "Premature optimization is the root of all evil"
  - Optimisation should be justified by profiles and benchmarks.
  - Prefer to write simple, clear code unless optimisation is *necessary*.
* Don't write sloppy code:
  - Avoid *unnecessary* allocation and copying.
  - Use iterators.
  - Don't use `Arc` or `Mutex` for single-threaded code (use `Rc` or `RefCell` instead).
  - Avoid global, mutable state.
  - Prefer to use `push` and `push_str` to build strings in performance-sensitive code, rather than the `format` macro.
  - Don't worry about empty Vecs/Strings - they don't allocate and are very cheap.
  - Consider the computational complexity of algorithms, but bear in mind the expected size of input.
* Don't hide potentially expensive code
  - code that may do IO, block, or sleep should be clearly named and documented.
  - allocation should be either expected from the purpose of a function or be documented (e.g., conversion functions should not allocate).

The concurrency of your code is likely to have a large impact on its performance.
Consider if and how code can be concurrent at the design phase.
Design to avoid locking or other forms of synchronisation if code is performance-sensitive.


## Rationale

* Modern compilers and CPUs can optimize simple code more effectively than complicated code; modern CPUs are complex, it is very difficult to predict whether hand-optimised code is effective.
* Allocation is a primary cause of slowdown in many programs.
* Use iterators to avoid slow bounds checks.
* Don't surprise clients of your code.
* Performant code requires scaling to multiple threads, blocking threads on locks, IO, etc., kills parallelism.

<p align="center">
<a href="unsafe.html">&lt;&lt; Unsafe code</a> | <a href="README.html">^ Contents ^</a>
</p>
