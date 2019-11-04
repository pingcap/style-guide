# Rust

## Contents

* [Introduction](#Introduction)
* [Formatting](formatting.md)
* [Naming](naming.md)
* [Comments](comments.md)
* [Modules and crates](modules.md)
* [Data structures](data.md)
* [Implementing traits](traits.md)
* [Functions, methods, and traits](functions.md)
* [Macros](macros.md)
* [Expressions and statements](exprs.md)
* [Error handling](errors.md)
* [Unsafe code](unsafe.md)
* [Performance](performance.md)


## Introduction

* Prefer to use 2018 edition idioms to 2015 idioms.
* Use Rustmt.
* Use Clippy.
  - TODO which lints should be used/not used, note disabled lints in `make clippy`, could we turn on some pedantic lints?

TODO

### Further reading

TODO

### Open questions

* error handling
* callbacks
    - could be futures?
    - performance if we need conversion layer (widely used)
* metrics?
    - global vs local (better perf) vs thread local metrics (maybe the best, but not widely used)
