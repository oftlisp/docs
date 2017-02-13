# Bootstrapping OftLisp

## Stage 1: `oftlisp-bootstrap`

`oftlisp-bootstrap` is an extremely simple bootstrapping compiler written in
Rust. `oftlisp-bootstrap` does not support `import`ing any modules except a
select few `std` modules. Additionally, it doesn't support macros or reader
macros other than `quote`. It takes the source of a file via `stdin`, and writes
cooresponding LLVM IR source to `stdout`.

Executables created by `oftlisp-bootstrap` do **not** use the same ABI as those
created by `oftlispc`. In general, don't use this for anything but bootstrapping
the language.

## Stage 2: `oftlisp-bi`

`oftlisp-bi` is the bootstrapping interpreter; its source is generated from
`oftlispi`'s source, and transformed to the reduced form of OftLisp that
`oftlisp-bootstrap` supports. It in turn supports the full OftLisp language,
including importing modules. It is then used to interpret `oftlispc`.

Note that `oftlisp-bi` uses a different main module from `oftlispi`, in the
interest of simplicity. It doesn't support loading the source from a file,
doesn't interpret until EOF, doesn't have color, and doesn't support
`libedit`/`readline`. The common point of similarity is the module loading and
evaluation portions of the code, which allow it to actually implement the
language.

`oftlisp-bi` is generated using a relatively simple script that essentially
resolves `import`s the same way JavaScript dependency managers do -- by placing
the `import`ed into a lambda which is executed at the top-level and returns its
imports. The returned imports are then set in the global scope. After this is
completed, macros are expanded. Note that this means that a macro that expands
to an `import` is invalid. A final pass is then performed to lift all `import`s
to the top of the output file and to ensure that they are all to `std` modules
implemented by `oftlisp-bootstrap`.

## Stage 3: `oftlispc`, Part One

`oftlispc` (being interpreted by `oftlisp-bi` at this point) then compiles
itself. See stage 4 for more details.

## Stage 4: `oftlispc`, Part Two

The `oftlispc` compiled in stage 3 is now a fully-bootstrapped and usable
OftLisp compiler. It uses `std/ffi` to bind to LLVM for code generation.

## Stage 5: `oftlispi`

`oftlispi`, the standard OftLisp interpreter, is then compiled by `oftlispc`. It
uses a relatively simple `eval` function, rather than JIT, to allow for
`oftlisp-bi` to be generated from it. It is a fully functional OftLisp system,
and should be considered just as "official" as `oftlispc`.
