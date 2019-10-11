# Formatting

Use [Rustfmt](https://github.com/rust-lang/rustfmt): `cargo fmt --all`;
do not customise rustfmt settings (rustfmt.toml), except for temporary work-arounds.
You can install Rustfmt using `rustup component add rustfmt`.

Rustfmt is not complete, there is some code where Rustfmt will not reformat code.
A common example is some bodies of `macro_rules!` items and some macro uses.
In these cases, follow the Rust formatting [style guide](https://github.com/rust-lang/rfcs/blob/master/style-guide/README.md).

When writing macro uses which Rustfmt does not format, if the content is code-like, try to follow the formatting style guide.
If the content is tabular, arrange the data in columns using spaces, not tabs, on multiple-of-four column positions.

### Rationale

* Make code consistent with the Rust ecosystem to lower barriers to entry.
* Make code self-consistent to make code reading easier.

## Imports

Rustfmt will format imports but permits several different styles.

* Avoid `extern crate`, except for macros which are used in many files (e.g., logging macros).
  Group `extern crate`s together.
* Group imports from the same logical project together (i.e., imports from different crates in the same project).
* Group imports from external crates together; do not separate `std` imports from other crates.
* Group re-exports together (`pub use ...;`).
* Prefer to use absolute paths to relative paths (i.e., `crate::..` rather than `super::...` or `self::...`).
  Exception: when re-exporting from sub-modules, use relative paths (e.g., `pub use self::foo::bar;`).
* Avoid glob imports.
  Only use a glob import when you mean "import lots of items from some module", not when you mean "I want lots of items from this module, but I don't want to write them out".
  Exception, if a crate provides a prelude module, import it using a glob import (e.g., `use futures::prelude::*'`).
* Use a single nested import, rather than multiple imports, for each crate.

Example:

```rust
TODO
```

### Rationale

* Ecosystem conventions.
* Modern conventions.
* Self-consistency.
* Make it easier to figure out where names come from without IDE support
* Make it easier to see which crates and modules a module depends on.



