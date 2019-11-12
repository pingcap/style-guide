# General advice

The guidelines in this section should apply no matter which programming language you are using.

* Be consistent with existing code.
* Prioritize maintaining code over writing code.
  Code is typically modified far more times than it is initially written.
* Prioritize refactorability of code over reuse.
  The YAGNI principle applies here and means that you probably won't need to reuse code.
  However, nearly all code gets changed and refactored at some point.
* Prefer simple code over clever code.
* Avoid (premature) optimisation until it is proved necessary.
  Optimized code is usually less clear to read and harder to maintain.
* In general, smaller abstraction units (functions, classes, modules, etc.) are better.
  Smaller units of code are easier to read and reason about.
* Don't repeat yourself (DRY principle).
  Duplicate code is harder to maintain.
  However, don't prioritize DRY over comprehensibility.
* Document liberally; self-documenting code doesn't exist.
  Make sure all documentation counts, don't add boilerplate documentation.
* Use code quality tools for formatting and linting; address all compiler warnings.
* Tests and benchmarks are code and should be written to the same high quality as other code.
  However, there are different priorities, so good code for tests may be different to good code for other source code.
* Ensure that all code supports all platforms.
  Take into account things like OS-specific functionality, SIMD, inline assembly, etc.
* Don't commit commented out code.
  It is messy and distracting, and unless well-documented, it is difficult to know why it is there.
* Use `FIXME` for notes that should be addressed in the future.
  `FIXME`s can be checked into source control.
  Every `FIXME` should have a reference to a GitHub issue describing the issue and an explanation, e.g., `FIXME(#2043): once we have X, we should do Y.`
* Use `TODO` for notes that should be addressed before a patch is finished.
  Do not commit `TODO`s into source control.
