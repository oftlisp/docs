+++
title = "Bootstrapping OftLisp"
+++

## Stage 1: `oftb` and `oftcesk`

`oftb` is an interpreter written in Rust, using a naive interpretation model.
Since it doesn't have tail-call, it's very slow and often overflows the stack on medium-sized programs.
However, it is capable of macro expansion.

`oftcesk` is a CESK-semantics-based interpreter, inspired by [Writing an interpreter, CESK-style](http://matt.might.net/articles/cesk-machines/).
It does not contain a language frontend; instead it runs a bytecode produced by `oftb`.

## Stage 2: `oftc-bootstrap`

`oftc` is an OftLisp-to-LLVM compiler written in OftLisp.
The `oftc-bootstrap` backend is used as a surrogate for proper FFI; instead, it outputs LLVM 3.9 IR in textual form.

## Stage 3: `oftc`

Once `oftc-bootstrap` is self-hosting, it makes sense to develop more backends.
A `std/ffi`-using [cretonne](https://github.com/stoklund/cretonne) or LLVM backend is the first priority.
At that point, the compiler is "finished."
