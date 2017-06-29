---
title: Bootstrapping OftLisp
---

# Bootstrapping OftLisp

## Stage 1: `oftb`

`oftb` is an interpreter written in Rust, which does *not* implement `std/ffi`.

## Stage 2: `ofti`

`ofti` is an interpreter written in OftLisp. It can be run without `std/ffi`.

## Stage 3: `oftc`

`oftc` is a compiler written in OftLisp, using `std/ffi` to bind to LLVM.
