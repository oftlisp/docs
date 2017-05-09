# OftLisp FFI System

> FFI is hard
> -- Me at 4:25 AM

## High-level

A high-level FFI package is provided at `std/ffi`. It supports the standard set
of signed and unsigned integers of various sizes (1, 2, 4, and 8 bytes), as
well as `size_t`, `ptrdiff_t`, etc. FFI definitions are performed in a
`def-ffi` block:

```
(def-ffi foo
  (options
    (define "ENABLE_BAR") ; Add -DENABLE_BAR flag
    (include-path "/usr/include/quux") ; Add -I /usr/include/quux to flags.
    (link "foo") ; Add -lfoo.
    (pkg-config libfoo)) ; Add flags from pkg-config --cflags and
                         ; pkg-config --libs.
  (fn foo (int int) int) ; int foo(int, int);
  (struct bar      ; typedef struct {
    (a int)        ;     int a;
    (b ptrdiff-t)) ;     ptrdiff_t b;
                   ; } bar;
  (fn baz (bar*) size-t) ; size_t baz(bar*);
  (macro "<errno.h>" EDOM int) ; There's a macro EDOM in the system errno.h,
                               ; with an intish type
  (macro "foo.h" XYZZY char*)) ; There's a macro XYZZY defined in a local
                               ; header foo.h, with a char* type.
```

Note that the macro block will involve parsing the specified headers; this
happens during macro expansion, so there's effectively zero runtime overhead.
This results in the following symbols being defined at the top level, with the
given types:

```
foo-foo : (fn ffi-int ffi-int ffi-int)
foo-bar : (fn (ffi-opaque foo-bar) ffi-int, ffi-ptrdiff-t)
foo-baz : (fn ffi-size-t (ffi-opaque-bar foo-bar))
foo-EDOM : ffi-int
foo-XYZZY : (ffi-pointer ffi-char)
```

A large number of utility functions are also defined, to convert the various C
numeric types to and from OftLisp types. Additionally, there are two string
conversion functions, `ffi-cstr-to-string` and `ffi-bytes-to-string`. The
former converts a null-terminated array of characters to a string. The latter
also takes a length. They have the types:

```
ffi-cstr-to-string : (fn string (ffi-pointer ffi-char))
ffi-bytes-to-string : (fn string (ffi-pointer ffi-char) ffi-size-t)
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
