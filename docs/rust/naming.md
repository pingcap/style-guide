# Naming

Prefer meaningful names to short names.

## Casing

* Types and traits: `UpperCamelCase`,
* Enum variants: `UpperCamelCase`,
  - Do not prefix with the enum name, e.g., `Some` not `OptionSome`,
* Struct fields: `snake_case`,
* Function and method names: `snake_case`,
* Local variables: `snake_case`,
* Macro names: `snake_case`,
* Constants (`const`s and immutable `static`s): `SCREAMING_SNAKE_CASE`,
* Crate names: `kebab-case` in Cargo and `snake_case` in Rust code (but prefer single word names where possible),
* Module names: `snake_case`.

Treat acronyms as words, e.g., `GrpcType` or `grpc_variable`, not `GRPCType` or `g_r_p_c_variable`/`GRPC_variable`.

When a name is forbidden because it is a reserved word (e.g., `crate`), use raw identifiers (e.g., `r#crate`).

Prefer using full words rather than abbreviations, e.g., `context` and `expansion_context` rather than `ctx` and `expn_ctx`.
Use abbreviations where the name is taken from the wider world where an abbreviation is standard, e.g., `Sql` rather than `StandardQueryLanguage`.
Treat abbreviations as a single word, even if it is a contraction of multiple words, e.g., `Stdin` rather than `StdIn`.

When interfacing with non-Rust code, prefer to use Rust conventions in the Rust code, rather than conventions from another language.
This may mean that items in Rust and non-Rust code have different names, but the correlation should be clear.
Where necessary, use aliases or wrappers to avoid using unconventional names for FFI items.

Generic type and lifetime parameters should usually have short (usually single letter) names, e.g., `T` and `'a`.
Use longer names for generics if there are multiple generics in scope and short names are confusing.
Associated types should have descriptive names like any other type.

## Method names

A primary (default) constructor function should be called `new`.
If the constructor takes no arguments, prefer to implement `Default` instead.
Secondary constructors (which customise values which take defaults in `new`) should be prefixed with `with_`, e.g., `with_capacity`.

A conversion method which preserves the original and gives a reference to a different type should be called `as_type` where `type` is the type being converted into.
E.g., `as_str`, not `as_str_ref` or `get_str` or `str`.
Such a conversion should be very cheap, usually just a different view on the original.

A conversion method which consumes the original and gives an owned value should be called `into_type`.
E.g., `into_string`.
An `into_type` method should not clone any part of the original (unless it is very cheap, e.g., `Copy` types).

An expensive conversion method (e.g., one which clones the original) should be called `to_type`.
E.g., `to_string` or `to_str`.

Where a smart pointer or other wrapper type has a method which consumes the wrapper and returns the inner type, the method should be called `into_inner` if it will never panic and `unwrap` if it might panic.
Where the wrapper is not consumed and a reference is returned, use `get`.
For other unwrapping methods, follow the naming conventions used by `Box`, `Option`, and `Result`.

The default method for iterating over the contents of a type should be called `iter`.
A method for iteration which consumes the collection should be called `into_iter`, however, such a method should usually be in an implementation of `IntoIterator`.

Prefer not to use getter and setter methods, but if you do they should be called `foo` and `set_foo` (where `foo` is the name of the field), not `get_foo`. Method for checking the presence of a field should have an `is_` or `has_` prefix and return a `bool`.

When naming traits: "Prefer (transitive) verbs, nouns, and then adjectives; avoid grammatical suffixes (like -able)".
E.g., `Copy`, `Send`, `Encode`.

Variations of methods which have different ownership or mutability properties from the default, should use suffixes to distinguish the variations.
E.g., `foo` for the default variation (whether that is owned or by-ref), `foo_ref` for a by-reference version, `foo_move` for a version which gives ownership, and `foo_mut` for a by-mutable-reference version.
Don't use multiple suffixes, e.g. `foo_mut_ref`.

### Rationale

Existing guidance:

* [Rust style guide](https://github.com/rust-lang/rfcs/blob/master/style-guide/advice.md#names)
* [Rust API design guidelines](https://github.com/rust-lang-nursery/api-guidelines/blob/master/src/naming.md)
* [RFC 199](https://github.com/rust-lang/rfcs/blob/master/text/0199-ownership-variants.md)
* [RFC 344](https://github.com/rust-lang/rfcs/blob/master/text/0344-conventions-galore.md)
* [RFC 430](https://github.com/rust-lang/rfcs/blob/master/text/0430-finalizing-naming-conventions.md)

<p align="center">
<a href="formatting.md">&lt;&lt; Formatting</a> | <a href="comments.md">Comments &gt;&gt;</a>
</p>
