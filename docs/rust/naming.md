# Naming

**Prefer meaningful names to short names.**

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

All names should be from English.

When a name is forbidden because it is a reserved word (e.g., `type`, `crate`), use an abbreviation (e.g., `ty`) or an underscore suffix (e.g., `crate_`).
Use raw identifiers (e.g., `r#type`, `r#crate`) if necessary; e.g., you are working with generated code or code which interacts with data using identifier names (e.g., JSON or protobuf libraries).

Prefer using full words rather than abbreviations, e.g., `diagnostic` and `expansion_config` rather than `diag` and `expn_cfg`.
It's ok to use abbreviations where they are well-known and standard outside the codebase (e.g., in the Rust or database communities).
E.g., `ctx` for 'context', `cf` for 'column family', or `expr` for `expression`.
When in doubt, use the full name.
Use acronyms where the acronym is standard, e.g., `Sql` rather than `StandardQueryLanguage`.
Treat acronyms as words, e.g., `GrpcType` or `grpc_variable`, not `GRPCType` or `g_r_p_c_variable`/`GRPC_variable`.
In particular, 'TiKV' should be capitalised as `Tikv`, e.g., `TikvServer`, not `TiKVServer`
Treat contractions of multiple words as one word, e.g., `Stdin` rather than `StdIn`.

Where it is clear from the wider context, if is fine to use short (even single character) names for local variables with very narrow scope.
Some examples:

```rust
// Argument to inline closure (`s`).
vec.iter().map(|s| s.len());

// Single line `match` arm (`e`).
match ... {
    Ok(_) => { ... }
    Err(e) => println!("Error: {}", e),
}

// Counter in `for` loop (`i`).
for (i, label) in labels.iter().enumerate() {
    ...
}
```

When interfacing with non-Rust code, prefer to use Rust conventions in the Rust code, rather than conventions from another language.
This may mean that items in Rust and non-Rust code have different names, but the correlation should be clear.
Where necessary, use aliases or wrappers to avoid using unconventional names for FFI items.

Generic type and lifetime parameters should usually have short (usually single letter) names, e.g., `T` and `'a`.
Use longer names for generics if there are multiple generics in scope and short names are confusing.
Associated types should have descriptive names like any other type.

## Method names

A primary constructor function should be called `new`.
A `new` constructor may take values for all fields or provide some defaults.
Secondary constructors (which usually customise more values than in `new`) should usually be prefixed with `with_`, e.g., `with_capacity`.

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
E.g., `foo` for the default variation (whether that is owned or by-ref), `foo_ref` for a by-reference version, and `foo_mut` for a by-mutable-reference version.
Don't use multiple suffixes, e.g. `foo_mut_ref`.

In most cases mutability does not need to be reflected in method names.
Where it is required to disambiguate between mutable and non-mutable return types, for example, use `mut` should be a prefix, not a suffix.
E.g., `as_mut_slice`, not `as_slice_mut`.

### Rationale

Existing guidance:

* [Rust style guide](https://github.com/rust-lang/rfcs/blob/master/style-guide/advice.md#names)
* [Rust API design guidelines](https://github.com/rust-lang-nursery/api-guidelines/blob/master/src/naming.md)
* [RFC 199](https://github.com/rust-lang/rfcs/blob/master/text/0199-ownership-variants.md)
* [RFC 344](https://github.com/rust-lang/rfcs/blob/master/text/0344-conventions-galore.md)
* [RFC 430](https://github.com/rust-lang/rfcs/blob/master/text/0430-finalizing-naming-conventions.md)

<p align="center">
<a href="formatting.html">&lt;&lt; Formatting</a> | <a href="comments.html">Comments &gt;&gt;</a>
</p>
