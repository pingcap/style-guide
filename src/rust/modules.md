# Modules and crates

TODO: including imports, exports, privacy, deps, licenses, publishing, stable vs unstable, small modules, modules in their own file, fancy imports, build scripts, cfgs

features - kebab case, non-negative

TODO see also formatting.md#imports

## What to import

TODO probably move to modules and crates section

* Prefer to import modules, types, and macros;
  prefer not to import functions and constants, use qualified syntax (except for very common functions, such as `std::thread::spawn`).
* Never import enum variants, qualify use with the enum name (except for very common variants such as `Some` and `Err`).
* Prefer to import a prelude module if available.
* Prefer to import macros rather than using `extern crate` and/or `macro_import`.


### Rationale

* Ecosystem conventions.
* Self-consistency.
* Make it clear at the use-site where a name is defined.
* Don't encourage boilerplate prefixes for enum variants.
* Modern conventions.
