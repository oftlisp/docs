+++
title = "The Macro System"
+++

OftLisp's macros are to a large degree based on those of Common Lisp; they use `defmacro`, and for now have no hygiene mechanism.

Macros are imported separately from standard declarations; see the [`modules.md`](modules.html) document for more details.

## Macro Processing

When a module is loaded, the `module` form and `import` forms are processed.
The modules specified in the `import` forms are then loaded.

The current module's macro list is then created, starting with the macros present in `std/prelude`, and then augmented with each of the imported macros in order.
An implementation may then choose to prune any shadowed macros, although in practice this is likely to be irrelevant for performance.
The body of the module is then processed, with the following simple algorithm:

As each form is encountered, the tree is walked downwards, looking up each symbol-headed list form in the macro list.
If the head symbol is present in the macro list, its corresponding macro is invoked.
The scope in which the macro is invoked contains only the declarations that have been imported.
This may be changed in the future, but the primary issue with allowing macros to call arbitrary global items is that the module's AST is not yet created.
After a macro is expanded, its output is inserted into the tree, and macro processing is run on it again.
This means that a macro that causes an infinite-loop in expansion is trivially possible:

```oftlisp
(defmacro infinite-loop ()
  '(infinite-loop))
```

If a `macro-multiple` form is found inside a `progn` (or `progn`-like context) after macro expansion, it is "spliced in." For example:

```oftlisp
(defn foo ()
  (def alpha 1)
  (macro-multiple
    (def beta 2)
    (def gamma 3))
  (def delta 4))
```

would become

```oftlisp
(defn foo ()
  (def alpha 1)
  (def beta 2)
  (def gamma 3)
  (def delta 4))
```

It is an error for a `macro-multiple` form to appear in a non-`progn`-like context.

Lastly, if a `defmacro` is found at the top level after all the previous steps, it is parsed and added to the macro list.
