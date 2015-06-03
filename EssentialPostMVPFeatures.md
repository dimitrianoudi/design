# Essential Post-MVP Features

This is a list of essential features that are known to be needed ASAP, but were removed from
[the MVP](MVP.md) since there was not (yet) a portably-efficient polyfill via asm.js. There is a much bigger
[list of features](FutureFeatures.md) that will be added after this list, prioritized by feedback and
experience.

## Threads
* Provide low-level buildings blocks for pthreads-style shared memory: shared memory,
  atomics + futexes (or [synchronics](http://www.open-std.org/jtc1/sc22/wg21/docs/papers/2014/n4195.pdf)).
* Import [SharedArrayBuffer proposal](https://docs.google.com/document/d/1NDGA_gZJ7M7w1Bh8S0AoDyEqwDdRh4uSoTPSNn77PFk).
  * The goal is to reuse the specification of memory model, happens-before, etc (with TC39) and backend implementation
    (same IR nodes and semantic invariants preserved).
* Modules can have global variables that are either shared or thread-local.
  * While the heap could be used for shared global variables, global variables are not aliasable
    and thus allow more aggressive optimization.
* Initially, a WebAssembly module is distributed between workers via `postMessage()`.
  * This also has the effect of explicitly sharing code so that engines don't
    perform N fetches and compile N copies.
  * May later standardize a more direct way to create a thread from WebAssembly.

## Fixed-width SIMD
* Essentially, import [SIMD.js](https://github.com/johnmccutchan/ecmascript_simd).
  * Would be statically typed analogous to [SIMD.js-in-asm.js](http://discourse.specifiction.org/t/request-for-comments-simd-js-in-asm-js).
  * The goal is to both reuse specification of op semantics (with TC39) and backend implementation (same IR nodes)
  * Track SIMD.js after the MVP.
* SIMD adds new primitive variable/expression types (e.g., `float32x4`) so it has to be part of
  the core semantics.
* SIMD operations (e.g., `float32x4.add`) could be either builtin ops (no different than int32 add) or
  exports of a builtin SIMD module.

## 64-bit integers
* Provide access to efficient 64-bit arithmetic.
* Some code will want to only use 64-bit integers when running on a 64-bit system (for performance
  reasons) so provide a "has native 64-bit integer" query.

## Zero-cost Exception Handling
* Developer access to stack unwinding and inspection.
* This may be used to implement `setjmp`/`longjmp` (instead of the usual
  opposite approach). This can enable all of the defined behavior of
  `setjmp`/`longjmp`, namely unwinding the stack, but does not allow
  the undefined behavior case of jumping forward to a stack that
  was already unwound (which is sometimes used to implement coroutines;
  however, explicit coroutine support is being considered separately
  anyhow).