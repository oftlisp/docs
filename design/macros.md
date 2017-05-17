---
title: The Macro System
---

# The Macro System

OftLisp's macros are to a large degree based on those of Common Lisp; they use
`defmacro`, and for now have no hygiene mechanism. The macro scoping rules are
based on Rust's; where a normal import might be written `(import foo (bar))`,
to import macros as well, `(import foo (bar) with-macros)` is written instead.
