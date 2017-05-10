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
between `-2147483648` and `2147483647` (`-2^31` to `2^31 - 1`).	On 64-bit
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

TODO Lists, functions, tuples

## Opaque Types
