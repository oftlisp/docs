---
title: The Module System
---

## Locating Modules

In OftLisp, each file is a module.
For example, the file `foo.oft` corresponds to the module `foo`.
The search path for modules is, in order:

 - `./.vendor`
 - `$OFTLISP_ROOT`
 - `$OFTLISP_HOME/src`
 - `.`

The `$OFTLISP_ROOT` variable has a compiled-in default, typically `/usr/lib/oftlisp` or `/usr/local/lib/oftlisp`.
In the future, an auto-detection mechanism may exist, so a recompile isn't needed in order to move the OftLisp installation.
Additionally, it may be overridden as an environment variable.
Typically, `$OFTLISP_ROOT` should only contain the standard library.

The `$OFTLISP_HOME` variable defaults to `$HOME/oftlisp`, although it can be overridden by the user as an environment variable.
The `$OFTLISP_HOME` should contain all downloaded modules.

## Anatomy of a Module

We will use the following module as an example:

```oftlisp
(module github.com/oftlisp/extstd/math/fibonacci
  fibonaccis
  is-fibonacci)

(import-macros github.com/oftlisp/extstd/testing/quickcheck
  quickcheck)

(import github.com/oftlisp/extstd/streams
  stream-map
  stream-prepend
  stream-unfold
  stream-until)

(defn (next-fibonacci-state prev-state)
  (def before (snd prev-state))
  (def last   (fst prev-state))
  (def next (+ last before))
  (list next last))

(defn (fibonaccis)
  (->> (list 1 0)
    (stream-unfold next-fibonacci-state)
	(stream-prepend 1)
	(stream-map fst)))

(defn (is-fibonacci n)
  (->> (fibonaccis)
    (stream-until \(< n $))
	(all \(/= $ n))))

(quickcheck (all-fibs-are-fibs (n fixnum))
  (is-fibonacci (stream-nth n (fibonacci))))
```

The first form in any OftLisp file must be a `module` form.
It declares the name of the module and its exported declarations.
Macros are exported implicitly.

Next, all the `import-macros` forms that are present occur.
These forms contain the name of the module to import from, followed by a list of imported macros, either as symbols (which import the given symbol under the same name) or as 2-item lists, in which case the macro named by the first symbol is imported under the name of second symbol.

The third "header block" is `import` forms, which are essentially the same as the `import-macros` forms, but they import declared values instead of macros.

The remainder of the file is either `defmacro`s or declarations.
A declaration is either a `def`, a `defn`, or a `progn` that contains only declarations.
If a `progn` is present, its declarations will be lifted to the global scope.
