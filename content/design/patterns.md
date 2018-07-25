+++
title = "Patterns"
+++

Pattern-matching is a pretty darn important feature for a language to have, so I wanna be pretty darn sure OftLisp's is well-designed.
It's also rather useful, given OftLisp's more object-oriented data model, to have extensible pattern matching, where one can match fairly arbitrarily.
Therefore, there are two types of patterns, literal and extensible.

## Literal Patterns

A literal pattern is just a symbol, to which anything will bind.
If the symbol is not `_`, a binding is created with that name in scope.

## Extensible Patterns

If a pattern is a symbol-headed list, the symbol at the head is used as the name of the pattern.
There must then be two macros in scope, with names of the form `pat-*-matches?` and `pat-*-bind`, where `*` is replaced with name of the pattern.

`pat-*-matches?` is called with two arguments, the tail of the pattern and the name of the variable whose value is being matched against.
It should emit code for an expression that evaluates to a boolean.

`pat-*-bind` is called with the same two arguments, plus a third, which is an expression to evaluate.
It should emit an expression that creates the appropriate bindings, then evaluates the expression.
The emitted code may result in undefined behavior if the corresponding `pat-*-matches?` code would return false.

### quote

The `quote` pattern only matches a value that is literally identical to its pattern.
For example, the following code panics if the call to `map` does not return `()`:

```oftlisp
(def '() (map 1+ nil))
```

The code definining the `quote` pattern is reproduced here, as an example.

```oftlisp
(defmacro pat-quote-matches? (pat sym)
  `(equals ,sym ',pat))

(defmacro pat-quote-bind (pat sym)
  '())
```

### quasiquote

The `quasiquote` pattern behaves largely as the `quasiquote` macro does, but with one crucial change -- an `unquote` or `unquote-splicing` form contains a pattern instead of an expressions.
Correspondingly, only one `unquote-splicing` is allowed per list/vector.

## defpattern

`defpattern` is used to create simple pattern aliases.
It takes three arguments, a pattern name, a list of arguments, and the pattern to expand to.
As an example, here we define patterns `(E)` and `(T l x r)` for matching binary trees:

```oftlisp
(defpattern E ()      'E)
(defpattern T (l x r) `(T ,l ,x ,r))

(def example-tree (T (T E 1 E) 2 E))
(progn
  (def (T (T _ x _) y z) example-tree)
  (assert-eq x 1)
  (assert-eq y 2)
  (assert-eq z 'E))
```
