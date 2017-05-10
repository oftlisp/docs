# OftLisp FFI System

## High-level

A high-level FFI package is provided at `std/ffi`. It supports the standard set
of signed and unsigned integers of various sizes (1, 2, 4, and 8 bytes), as
well as `size_t`, `ptrdiff_t`, etc. FFI definitions are performed in a
`def-ffi` block:

```
(def-ffi foo
  (options
    (define "ENABLE_BAR") ; Add -DENABLE_BAR flag.
    (include-path "/usr/include/quux") ; Add -I /usr/include/quux flag.
    (link "foo") ; Add libfoo.so/foo.dll to symbol search list.
    (pkg-config libfoo)) ; Add flags from pkg-config --cflags and
                         ; add libraries from pkg-config --libs to symbol
                         ; search list.

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
foo-foo :: (fn ffi-int ffi-int ffi-int)
foo-bar :: (fn (ffi-struct foo-bar) ffi-int ffi-ptrdiff-t)
foo-baz :: (fn ffi-size-t (ffi-struct foo-bar))
foo-EDOM :: ffi-int
foo-XYZZY :: (ffi-pointer ffi-char)
```

A large number of utility functions are also defined, to convert the various C
numeric types to and from OftLisp types. Additionally, there are two string
conversion functions, `ffi-cstr-to-string` and `ffi-bytes-to-string`. The
former converts a null-terminated array of characters to a string. The latter
also takes a length. They have the types:

```
ffi-cstr-to-string :: (fn string (ffi-pointer ffi-char))
ffi-bytes-to-string :: (fn string (ffi-pointer ffi-char) ffi-size-t)
```

## Low-Level Dynamic FFI Calls

FFI in OftLisp may be implemented with the `std/ffi/raw` module. This module
contains bindings to (libffi)[https://sourceware.org/libffi/] and libdl. A
typical FFI call might look something like:

```
(import std/ffi/raw (dlopen dlsym ffi-call make-cif))

(def cif (make-cif 'double '(double)))

(def libc (dlopen nil))
(def cos (dlsym libc "cos"))

(call cif cos 1.234)
```

This interface is significantly slower than a compiled FFI call, but also much
more flexible -- the name of the function need not statically be determined,
and neither does the library they are loaded from, or their type.

## Compiled FFI Calls

It is of course possible to compile an FFI call, and `oftc` does. In this case,
the library will be dynamically *linked* instead of dynamically loaded. The
largest distinction is that an error due to a library not being present will
happen on program initialization rather than on the attempted use of one of the
functions.

This presents an issue when a library is intended as an optional feature. This
is simply not possible when dynamic linking is used. Therefore, one supported
option to the `options` clause in `def-ffi` is `optional`. When this option is
present, the functions emitted will always be the ones based on `std/ffi/raw`.
When it is not, `oftc` will modify `def-ffi` to cause it to emit special stubs
that call the requested foreign functions.
