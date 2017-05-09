# OftLisp FFI System

> FFI is hard
> -- Me at 4:25 AM

## High-level

A high-level FFI package is provided at `std/ffi`. It supports the standard set
of signed and unsigned integers of various sizes (1, 2, 4, and 8 bytes), as
well as `size_t`, `ptrdiff_t`, etc.

```
TODO
```

## Low-level

FFI in OftLisp is implemented with the `std/ffi/raw` module. This module
contains bindings to (libffi)[https://sourceware.org/libffi/]. A typical FFI
call might look something like:

```
(import std/ffi/raw (dlopen dlsym ffi-call make-cif))

(def cif (make-cif 'double '(double)))

(def libc (dlopen nil))
(def cos (dlsym libc "cos"))

(call cif cos 1.234)
```
