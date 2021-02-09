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

You use Rustfmt and Clippy.
You should use Rustfmt with no customisation (rustfmt.toml file).
You should use Clippy with the same set of disabled lints as TiKV.
The easiest way to do that with TiKV is to use `make clippy` rather than `cargo clippy`.

Idiomatic Rust programming changed with the 2018 edition.
For example, how imports are written and which lifetimes can be elided.
You should always use Rust 2018, and should use the modern idioms (such as avoiding `extern crate`, importing macros using `use`).
Be aware when reading older code that it may be from pre-2018 edition.

When writing Rust where there are competing conventions, e.g., with FFI and C or interacting with protocol buffers, stick to the Rust conventions rather than the domain conventions.
E.g., types which are wrappers around C types should have CamelCase names even if the underlying types have snake_case names (though the bindings themselves will have the C names).


### Further reading

The following resources were useful in making this guide, there is often more detail than could be accommodated here.

* [Formatting guide](https://github.com/rust-lang/rfcs/tree/master/style-guide)
* [API design guidelines](https://github.com/rust-lang-nursery/api-guidelines)
* [Killercup's style guide](https://deterministic.space/elegant-apis-in-rust.html)
* [Rust design patterns](https://github.com/rust-unofficial/patterns)
* [The nomicon](https://doc.rust-lang.org/nomicon/index.html)
* Convention RFCs in the [RFC repo](https://github.com/rust-lang/rfcs).

### Open questions

FIXME: address these

* error handling
* callbacks
    - could be futures?
    - performance if we need conversion layer (widely used)
* metrics?
    - global vs local (better perf) vs thread local metrics (maybe the best, but not widely used)
* could we turn on some pedantic lints or use some rustfmt options?
  - [Rustfmt](https://github.com/rust-lang/rustfmt/blob/master/Configurations.md)
    * merge_imports
    * merge_derives
    * condense_wildcard_suffixes
    * newline_style = "Unix"
    * normalize_comments
    * normalize_doc_attributes
    * reorder_impl_items
    * report_todo
    * unstable_features
    * use_field_init_shorthand
    * use_try_shorthand
  - [Clippy](https://rust-lang.github.io/rust-clippy/master/index.html)
