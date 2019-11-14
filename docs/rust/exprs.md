# Expressions and statements

Clippy has a lot of advice for writing statement-level code.
See [their lint list](https://rust-lang.github.io/rust-clippy/master/), but bear in mind that some are lints are overly pedantic.

Don't use `println` or similar for logging/debugging (at least in committed code).
Use a logging library.

Use variable shadowing judiciously, usually where the values are logically the same but referenced in different ways.
Using the same name for logically different values should be avoided.
For example, `let x = ...; ...; let x = Rc::new(x);` is a good use of shadowing.
Rationale: variable shadowing can lead to very confusing code when used irresponsibly.

If you are not capturing any of the environment, prefer to use functions over closures.
Rationale: makes reading code easier since there is no possibility of implicitly scoped variables.

Use [`std::mem::replace`](https://doc.rust-lang.org/nightly/std/mem/fn.replace.html) to move data out of a mutable reference.
See also the [`mem::replace` to keep owned values in changed enums](https://github.com/rust-unofficial/patterns/blob/master/idioms/mem-replace.md) pattern.

Prefer expression-oriented to statement-oriented code.
See the [Rust style guide](https://github.com/rust-lang/rfcs/blob/master/style-guide/advice.md#expressions).

Avoid putting complex code in the conditions of `if`, `match`, and similar expressions.
Use a named variable instead, it is almost always clearer.

Use `into` rather than `from` where you have the choice.

Avoid using `let _ =` to address an unused result warning.
Prefer to use `?` or `unwrap`.

Be aware that `let _foo =` and `let _ =` have different semantics; the latter does not create a scoped variable.
This means that if the value has a destructor, it will be called immediately for the second form and at the end of the scope in the first form.

Use the [`Entry` APIs](https://doc.rust-lang.org/nightly/std/collections/hash_map/enum.Entry.html).

Prefer to use an explicit type on a variable rather than a turbofish where possible.
Prefer to use turbofish rather than type ascription.
E.g., prefer `let x: Vec<_> = iter.collect();` over `let x = iter.collect::<Vec<_>>();`

Use parentheses rather than relying on operator precedence.
Rationale: easier to read and when used regularly, avoids some hard to spot bugs.

Use field initializer shorthand where possible, e.g., prefer `Foo { x, y }` to `Foo { x: x, y: y }`.

## Mutability

Prefer to use variable shadowing to mutability where possible.
Rationale: by restricting use of mutability, you are less likely to accidentally mutate a value, and more likely to benefit from the mutability lints catching errors in your code.

Minimize the scope of mutability by using variable shadowing and/or explicit scopes: `{ ... }`.
See also the [temporary mutability](https://github.com/rust-unofficial/patterns/blob/master/idioms/temporary-mutability.md) idiom.


## Iteration

Avoid iterating over a collection without using an iterator.
It has poor performance and is error-prone.

Prefer to use iterator combinators (`map`, `filter`, etc.) to imperative iteration (e.g., using a `for` loop).
However, use imperative iteration if it is clearer due to large closures or side-effects.

Avoid using `collect` to force iteration if the result is not needed.
Use `for_each` or an imperative loop instead.
Rationale: clearer intention and does not require allocation.


## `match` and `if`

Avoid using `match` where possible by using method calls.
This is especially true for `Option` and `Result` which have a rich set of combinator methods.

Avoid pattern-matching on all fields in a struct - this is a backwards compatibility hazard.

Prefer to list patterns explicitly rather than using a wildcard (e.g., `_`) pattern.
Rationale: fail fast if an `enum` changes.

Prefer to let the compiler handle borrowing, rather than explicitly using `ref` patterns.
See the [match ergonomics RFC](https://github.com/rust-lang/rfcs/blob/master/text/2005-match-ergonomics.md) for details.

If you have multiple wildcards (`_`) in a pattern, condense them into a single `..`.

Prefer to use `if let` rather than `match` with a single arm.
Prefer to use `match` rather than `if let ... else`.
Rationale: use the clearest, most concise code possible.

In an `if ... else` expression, but the positive condition first, e.g., `if foo { a() } else { b() }` rather than `if !foo { b() } else { a() }`.

Prefer a guarded early return to an `if ... else` expression where one block is large and one block is small.
E.g., prefer

```rust
fn foo(x: i32) -> u8 {
    if x != 0 {
        return 42;    
    }

    // ...
    0
}
```

to

```rust
fn foo(x: i32) -> u8 {
    if x == 0 {
        // ...
        0
    } else {
        42
    }
}
```

## Panicking

Don't 'hide' panicking code; make it as explicit as possible by using naming and documentation.

See [error handling](errors.md) for more on `unwrap` and similar functions.

Prefer `debug_assert` to `assert` if the condition should always be true.
Use `assert` if the condition may reasonably be false due to an error or bug (even if unlikely).
If the condition might occur with no bug in code or 'impossible' error in data, then check the invariant without panicking.
