# Unsafe code

* Avoid unsafe code where possible.
* If using `unsafe` for performance, there must be evidence from benchmarks that there is a significant improvement.
* Be very careful about undefined behaviour - unsafe code has stricter semantics than C.
* Prefer `MaybeInit` to any use of uninitialized memory.
  - Almost any use of uninitialized memory will lead to UB.
* Where possible encapsulate `unsafe` code inside a safe function which re-establishes invariants before returning.
* Be especially careful with `unsafe impl`s, since these can have safety effects far from the declaration.
* Avoid `transmute` where possible
* If you use transmute, you should specify both type parameters using the turbofish.
  E.g., `let x = mem::transmute::<&'b R<'static>, &'b R<'c>>(r);`.
* Unsafe code should take extra care to validate its input.
* Make unsafe blocks as large or small as convenient.
  In the past it has been considered good practice to either make them as small as possible or to include any code which establishes the safety invariants.
  It turns out that unsafety is inherently non-local, so just use whatever scope best fits the code.
* Use Rust bindgen to generate FFI where possible, rather than writing FFI code by hand.
* The `mem` and `ptr` modules in std are often useful, prefer to use functions from these modules rather than doing lower level pointer manipulation, etc.

See also the [nomicon](https://doc.rust-lang.org/nomicon/).

## Documentation

* Document *every* use of `unsafe`.
  - Even trivial uses should have a comment stating why it is safe.
  - Including `unsafe impl`.
* Document why unsafe blocks are safe and how Rust's invariants are ensured.
* For unsafe functions, document the invariants the caller must ensure, and any restrictions on the input, or how the output may be used.


## Unsafe boundaries

Ensuring that unsafe code is safe, is a matter of ensuring that certain invariants are maintained whenever unsafe code is used.
(I use the term 'invariant' a bit loosely, including pre- and post-conditions).

The following are invariants of Rust:

* If there are multiple references to a value, it must be immutable.
* If a value is mutable, any reference to it must be unique.
  - Corollary: memory may not be accessed at the same time from more than one thread if any of those accesses are writes.
* Pointers of any kind (except raw pointers) may not be null.
* Any pointer of type `&T` (or any other 'pointer to `T`' type), then the pointer must point to initialized memory of at least the size of `T`.

These invariants must be maintained *all the time*, whether in safe or unsafe code (these don't have to hold in C code (or other languages) but must be re-established before returning to Rust code).
I.e., the boundary for establishing these invariants is the FFI boundary.
You don't need to document these invariants since they must always be maintained
However, it might be useful to document why they hold in specific cases or *how* they are maintained.

Most uses of unsafe code also have program-specific invariants (sometimes there are no extra invariants, for example with simple FFI calls, all input and program state (assuming the Rust invariants) might be valid).
If any of these invariants are broken, then either the above Rust invariants may break (leading to undefined behaviour or memory unsafety) or logic errors might occur *anywhere in the program* (i.e., not just in unsafe code).
These invariants must always be documented.

Some invariants can be guaranteed locally.
E.g., an unsafe block which calls an FFI function with a pointer-to-`T` and integer (`n`) input might have the invariant that the pointer points to at least `n * sizeof(T)` bytes of initialized memory (or `n` valid objects of type `T`).
The boundary for maintaining such invariants is the unsafe block itself.
In such instances, the unsafe code should be wrapped in safe code which establishes the invariants.
Usually, that safe code should be as small as possible (i.e., it *only* establishes the invariants).
E.g., the unsafe code in the previous example could be inside a safe function which takes a Rust slice and turns that into the pointer and integer:

```
/// Call the `foo` function defined in `some_library/foo.h`.
///
/// `Bar` must be `repr(C)`.
fn call_foo(input: &[Bar]) {
    let ptr = input.as_ptr();
    let len = input.len();
    // Calling `foo` is safe since `ptr` and `len` are a valid representation of `input`.
    unsafe {
        let result = foo(ptr, len);
        // `foo` will never return an error if the input is valid.
        debug_assert_eq!(result, 0);
    }
}
```

Note that even though the function is tiny and safe, we still document why the unsafe block is safe.
Here we make `ptr` and `len` outside the unsafe block, but check the result of the call inside.
This is not really important; we could do either work inside or outside the `unsafe` block.

A good approach for using unsafe FFI is to write a minimal Rust layer which converts between Rust and C types and establishes safety invariants.
Other Rust code should then use this safe facade, rather than using the generated bindings directly.

More complex invariants involve multiple unsafe blocks, i.e., a bug in one unsafe block (or its input) might manifest as an error in another.
In such cases the unsafety boundary is the *module*.
It is a good idea to make such a module as small as possible.
Use Rust's privacy mechanism to enforce this boundary.
I.e., public functions should be safe to call with any input and internal state, public functions should not be unsafe; private functions may be unsafe, or may be safe but rely on an invariant over the module's internal state (if the function can cause an error due to invalid input, then it should be unsafe).

The standard library's `Vec` is an implementation of this done well.
The [`vec`](https://github.com/rust-lang/rust/blob/master/src/liballoc/vec.rs) module contains very little unsafe code, and that unsafe code depends on fairly simple validity invariants which are easy to maintain for callers of the few unsafe functions.
Underneath `vec` is the [`raw_vec`](https://github.com/rust-lang/rust/blob/master/src/liballoc/raw_vec.rs) which has much more complex invariants and more unsafe code, but again this is well-encapsulated.
Note that the style of documentation is not quite what we use in TiKV - prefer to document invariants the module level, as well as on unsafe functions, and to document the safety invariants of every unsafe block.
