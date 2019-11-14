# Macros

Be mindful that macro definitions are often much easier to write than to read.
Prefer to use other abstraction mechanisms (functions, traits) where possible.
Don't use a macro unless there is substantial benefit (usually in significantly reducing boilerplate).

Prefer to use declarative (`macro_rules`) macros rather than procedural macros.

Prefer to use existing libraries (e.g., Serde) rather than writing new macros.

If writing procedural macros, use the [Syn](https://github.com/dtolnay/syn) and [Quote](https://github.com/dtolnay/quote) libraries for parsing and quasi-quoting, respectively.

Do not use the 'proc macro hack' to use procedural macros in item position.

Custom derive macros should not do complex code generation (use procedural macros it attribute position instead).
Mostly custom derives should only be used to automatically derive traits.
Custom derives can also be used to generate very simple functions and methods (e.g., `new` constructors ((derive-new)[https://github.com/nrc/derive-new]; (derive-more)[https://jeltef.github.io/derive_more/derive_more/constructor.html]) or getters and setters ((Getset)[https://github.com/Hoverbear/getset], though in general you should prefer public fields over generated getters and setters)).

### Formatting

Rustfmt will sometimes not format macro uses or definitions.
In such cases try to follow Rustfmt conventions as closely as possible.
See the [Rust style guide](https://github.com/rust-lang/rfcs/tree/master/style-guide) for a specification.
