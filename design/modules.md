# The OftLisp module system

## Overview

OftLisp's modules are arranged in a simple hierarchy based on URLs. The only
exception to this hierarchy is the `std` module, which is discussed in its own
section.

## `std`

The standard library is contained within the `std` module, and is implemented in
the (`oftlisp-runtime`)[https://github.com/remexre/oftlisp-runtime] repository.
The `std` module itself acts similarly to the Prelude of many other languages,
and is automatically imported. Additionally, many functions in `std` may be
treated as intrinsics by the compiler. (Note: This does not preclude them being
used as, for example, the functional argument to `map`, `fold`, or `filter`;
their properties as intrinsics are system-specific, and their existence as
functions is unaffected.)

Within the `std` module, the `std/internal` module deserves a special mention.
This module is completely unspecified, and should only be used by `std` to
implement its own functions.

The sole exception to the above property is the `main` function in the
`std/internal` module. This function is the main entry point to the program.
This function has the mangled name `oft3std8internal_main`.
