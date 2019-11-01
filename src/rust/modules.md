# Modules and crates

TODO

where to put test-only code?
    cfg mod vs item

impl in different module


## Module structure

* Modules should be small, but usually larger than a single item.
  - A module should have a single concept, usually a single data type, its `impl`s, and its supporting types and functions.
    Alternatively, a module might be a small collection of functions with a single, coherent theme.
* `util` modules are often a code smell and can be split up and given better names.
* Most modules should be declared in their own file.
  Modules consisting of only tests (`mod test { ... }`) and small modules used primarily as unsafe boundaries, may be declared inline.
* Modules should always have module-level documentation.
* Nested modules must be intuitively part of their enclosing module.
* Put attributes (e.g., `#[cfg(...)]`) on the module declaration (`mod foo;` or `mod foo { ... }`); do not use inner attributes (e.g., `#![cfg(...)]`).

TODO what should be in a prelude?

### Rationale

* Community conventions.
* Module-level docs proved very useful in rustc.


## Privacy and visibility

* Encapsulation in Rust works at the module-level, don't attempt OO-style encapsulation per-data type.
  Keep in mind that everything in a module and its submodules can read and write private data.
* Try to avoid complex visibility modifiers (`pub(...)` where `...` is `super` or any path; `pub(crate)` is ok, but plain `pub` or private is better).
  These are fine in some rare circumstances, but in general are a code smell indicating that modules could be better organised.
  - Rationale: simple is better and complex visibility modifiers can be hard to understand.
    Avoiding them seems to lead to simpler code in practice.
* Keep in mind privacy is inherited - a public item inside a private module is private outside that module unless it is re-exported; that means that using `pub(crate)` (or any path not inside the private module) is unnecessary and should be avoided.
* Be clear about why an item or field is private - keeping `unsafe` code safe or maintaining other internal invariants are good reasons; a vague feeling of encapsulation is not (e.g., public fields are usually better than using getters and setters).
  - Rationale: Rust convention is to prefer public fields to encapsulation where possible.
* Make submodules public where the module hierarchy should be part of the API (e.g., submodules in std).
  Make submodules private where they are logically part of the enclosing module (i.e., the module hierarchy is an implementation detail); use re-exports (`pub use`) to expose API from the submodules.
  - This includes the case where the crate is the enclosing module.
  - Re-export from a crate where the re-exported items are logically part of the crate-level module.
  - In small crates, it is often best to re-export the entire API and make all submodules private.
  - In larger crates, use public submodules in the API and only have minimal items or re-exports at the crate level (consider using a prelude module in this case).
* Re-exporting items from dependent crates is fine if the item is logically part of the API of the re-exporting crate.
  Do not do this just for convenience.
  Facade crates are usually an anti-pattern.
  - Rationale: this worked [badly](https://aturon.github.io/tech/2018/02/06/portability-vision/) for the Rust standard library, and some other libraries.


## What to import

* Prefer to import modules, types, and macros;
  prefer not to import functions and constants, use qualified syntax (except for very common functions, such as `std::thread::spawn`).
* Where a module is used for a single impl and many names in that impl would need to be imported, import only the module and use qualified names.
  A common example is implementing the `std::fmt::Debug` or `Display` traits, import `std::fmt` and use `fmt::Formatter`, `fmt::Result`, etc.
* To disambiguate names, prefer to use qualification, rather than renaming.
  E.g., use `fmt::Result` rather than `use std::fmt::Result as FmtResult`.
* Never import enum variants, qualify use with the enum name (except for very, very common variants such as `Some` and `Err`).
* Prefer to import a prelude module if available (using a `*` import).
* Prefer to import macros rather than using `extern crate` and/or `macro_import`.
* Prefer to import names at the module level.
  Only import names inside a function where the name is only used in that function and would cause a name clash if imported at the module level.

See also [formatting/imports](formatting.md#imports).

### Rationale

* Ecosystem conventions.
* Self-consistency.
* Make it clear at the use-site where a name is defined.
* Don't encourage boilerplate prefixes for enum variants.
* Make names and imports easy to trace manually.


## Crates

* Prefer small crates, but don't worry too hard about it.
* Every crate should have some crate-level documentation in their top-level module and in a README.md.
* Use a Cargo workspace where there are multiple crates in one logical project.
* If using a workspace, either have one top-level crate, or no to-level crates and one crate with the same name as the workspace.
* If publishing a workspace crate, minimise the number of crates in the workspace.
* Use build scripts for tasks which are part of every build (prefer Cargo build scripts to makefiles or other scripts).
* Use appropriate directives in build scripts (e.g., `println!("rerun-if-changed={}", some_file)`).
  See the [Cargo book](https://doc.rust-lang.org/cargo/reference/build-scripts.html) for details.
* Treat build scripts like code - follow engineering best practices and this guide.
* One-off actions should usually be OS scripts unless they need some Cargo functionality.
* Use [semver](https://semver.org/) versioning.
  Be aware that many minor changes can be breaking in Rust (e.g., *adding* a method to a trait).
* Export a minimal and coherent API.
  See the debuggability, dependability, flexibility, interoperability, and predictability in the [Rust API guidelines](https://github.com/rust-lang/api-guidelines/tree/master/src).
* Prefer to avoid unstable features.
  - Avoid unstable features in published crates.
  - Unstable features are ok in crates which are not published, if there is a large benefit.
  - If using unstable features, check the tracking issue:
    * is the feature on a path to stabilisation? If a feature is likely to be stabilised soon, then using it is probably ok.
    * Is the feature experimental or known to have bugs? Do not use these features.
    * If in doubt check with a Rust expert.
* Procedural macro crates should be named 'foo-macros', where 'foo' is the main crate they support.
* If any procedural macros depend on other crates, then use the following structure:
  - assuming we have crates foo and foo-macros, and a macro in foo-macros depends on a crate bar,
  - do not generate `extern crate bar;` from macros;
  - re-export bar and any public macros in foo-macros from foo;
  - use `::foo::bar...` in any macros;
  - the user should only depend on foo, not directly on foo-macros.
  - Rationale: This prevents users having to depend on bar.

### Rationale

* Small crates often improve compile times by improving parallelism.
  However, this effect is often small and will be smaller in the future, so we shouldn't prioritise it over good design.
* Publishing workspace crates is frustrating.
* Follow [Rust API guidelines](https://github.com/rust-lang/api-guidelines/tree/master/src).


## Dependencies

There are costs and benefits to adding dependencies, both small and large.
There are few absolutes, but here are some things to consider when adding a new dependency:

* Adding a dependency where we use most of the crate will be better for compile times than implementing the code inside an existing crate.
* A dependency where we use a small proportion of the code will be worse for compiles than implementing the code inside an existing crate.
* A well-used and maintained crate is likely to be more robust and have fewer bugs than code we write from scratch.
* Every (external) dependency introduces a small risk (security, maintenance, etc.).
* A crate which is already a transitive dependency is almost cost-free.
* LTO means that adding a dependency has a low cost in performance and code size (c.f., inline code).

Some other considerations for dependencies:

* Security-sensitive dependencies should be properly audited.
* You should do a basic audit of every new dependency.
  You should check:
  - Is the license compatible?
  - How many users does the crate have?
  - Is the crate actively maintained?
  - Does the crate have good tests and CI?
  - Are there better alternatives?
  - Does the crate support all platforms we want to support?
  - Does the crate use unsafe code?
    How much?
    Does it look reasonable?
    (Note that you don't need to verify every use of `unsafe`, but do try to get a general idea of the crate's quality).
* Use `cargo audit` to check for known security issues.
* Minimise duplication due to different versions.
* Use the most up-to-date version of a crate possible.
* Use `dev-dependencies` and `build-dependencies` where possible and appropriate.
* Make dependencies optional wherever possible.
* Path dependencies should only be used for workspace crates.
* Prefer using published versions of dependencies rather than Git dependencies.
* Use the simplest version possible.
* If you must use a Git dependency, always include a `rev`.

### Rationale

* Minimize compile times.
* Avoid risk due to old or unmaintained code, and by minimizing the surface area of external code.
* `cargo update` should not cause any breaking changes.


## Cargo.toml

* Keep dependencies in alphabetical order.
  - Rationale: make it easy to scan dependencies.
* Follow this order for sections:
  - `[package]`,
  - `[lib]`s and `[[bin]]`,
  - `[features]`,
  - `[workspace]`,
  - `[dependencies]`, `[*.dependencies.*]`,
  - `[dev-dependencies]`, `[*-dependencies]`,
  - `[replace]`, `[patch]`,
  - `[profile]`s.
* Sections should be separated by a single blank line.
* Limit lines to 100 characters.
* When using a multi-line block, put the opening and closing braces or brackets on their own lines, and indent with four spaces.
  - No other indentation.
* Where possible, prefer `[patch]` to `[replace]`.
* Prefer to use `foo = { ... }` to `[dependencies.foo]`.
* These package keys are required:
  - `name`,
  - `version`,
  - `authors = ["The TiKV authors"]`,
  - `license = "Apache-2.0"`,
  - `edition = "2018"`.
* Crates which are published on crates.io must also have:
  - `description`,
  - `keywords`,
  - `homepage`,
  - `repository`,
  - `readme`.
* Crates which are not published on crates.io must also have:
  - `publish = false`.
* Features should never be negative (e.g., `foo` is good, `no-foo` is bad).
  - Rationale: features are combined across the dependency graph by Cargo.
* Features should be named using kebab-case.
* Prefer short but descriptive feature names.
* Prefer not to have default features.
  - Only crates which have binaries or are otherwise user-facing (i.e., not workspace crates intended to be private dependencies) should have default features.
  - Rationale: they can cause complications with complex dependency graphs, and Cargo has some bugs with default features.

