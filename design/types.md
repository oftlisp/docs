# OftLisp Types

## Overview

OftLisp uses a type system based on the Dialyzer type-checker for Erlang, with
some additional complexity -- some values can have more than one type, due to
dynamic Lisps not having a distinction between lists and tuples. In addition,
numeric literals can have one of many types.

As a concrete example, the literal `("foo" "bar")` could have either
`(list string)` or `(tuple string string)` as its type. Even worse, the literal
`(1 2 3)` could have 350 different types -- one for a list of each of the seven
numeric types, or 3-tuples where each member could be each of the seven numeric
types. There would therefore be `7 + (7 * 7 * 7) = 350` possibilities.

## Basic Types

### Numeric Types

There are seven numeric types in OftLisp: `fixnum`, `ratio`, `bigint`,
`rational`, `float`, `complex`, and `crational`.

`fixnum` is a machine-sized signed integer. It must be able to hold all values
between `-2147483648` and `2147483647` (`-2^31` to `2^31 - 1`). On 64-bit
platforms, it is typically able to hold all values between
`-9223372036854775808` and `9223372036854775807` (`-2**63` to `2**63 - 1`).
This should not be relied upon, however.

`ratio` is a ratio between two integers of the same size as a `fixnum`. The
numerator is signed, while the denominator is unsigned. It will always be
normalized; that is, `(/ 2 4)` will result in `1/2` rather than `2/4`.

`bigint` is a integer with infinite range, positive or negative.

`rational` is a ratio between two `bigint`s. It will always be normalized.

`float` is a IEEE 754 double-precison floating point number.

`complex` is a complex number formed from two `float`s.

`crational` is a complex number formed from two `rational`s.

### Other Primitives

There are also several other primitive types: `string`, `symbol`, and `unit`.

`string` is an immutable UTF-8 string. If a mutable string is wanted, look in
`std/structures/cord`.

`symbol` is a memoized string stored in a special heap, the symbol heap. These
strings are not garbage collected, so care should be taken not to dynamically
create symbols over the course of the program's lifetime.

The `unit` value is the type of `nil`.

### Higher-Order Types

There are several types that are "higher-order"; that is, they take parameters.
These include functions, lists, taggeds, and tuples.

The function type constructor, written `fn`, takes at least one argument. All
of the arguments must be types. The first argument it takes is the return type
of the function, and all other arguments are the arguments a function takes,
from left to right. For an example, the type of a function that takes two
arguments, a string and a fixnum, and returns a float would be written
`(fn float string fixnum)`.

The `list` type constructor takes a single argument, which must be a type.

The `tagged` type takes at least two arguments. The first argument is a literal
symbol, while the others are types. A `tagged` is simply a variation of a tuple
where every value starts with the same symbol. For example, the value
`(foo 1 "bar" 2)` is a member of the type `(tagged foo fixnum string fixnum)`.

The `tuple` type takes at least one argument. All of the arguments must be
types.

## Opaque Types

All types not representable in OftLisp source code as literals are termed
"opaque." (The function type is a bit special; it is not considered opaque
because the `fn` form is compiled as a function literal.) These include a
number of data structures, FFI types, IO streams, and the like.

## Miscellaneous Types

The `any` type is the universal type; that is, all values may belong to it. It
is *never* type-inferred, and requires a type annotation to be used.

The `void` type is the empty type; that is, there are no values that belong to
it. It may be inferred for the return type of a function that loops infinitely.

TODO EXCEPTIONS?

A special type constructor of note is the `union` type constructor, which takes
two or more types as its arguments. Any value that is a member of any of the
argument types is a member of the `union`ed type. This is useful as a way to
implement algebraic datatypes; where `foo` is a zero-argument function that
returns either a `fixnum` on success or a `string` on error, the return type of
`foo` is written:

```
(union
  (tagged ok fixnum)
  (tagged err string))
```

Currently, it is required that the arguments of `union` all be `tagged` types.

## Type Annotations

The `::` special form is used to manually annotate the type of a variable. It
also yields the value of that variable, allowing it to be used for returns. An
example of its usage would be:

```
(defn foo (x y)
  (:: x (union
    (tagged bar ratio)
    (tagged baz any)))
  (:: y void)
  (:: 0 complex))
```

This would cause `foo`'s type to be inferred as
`(fn complex (union (tagged bar ratio) (tagged baz any)) void)`.
