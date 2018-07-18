+++
title = "Composable Type Systems"
+++

## Introduction

One of the goals of OftLisp is to be able to make a complete Lisp system that runs on bare-metal (i.e. as an operating system).
To that end, it makes a lot of sense to be able to have multiple sublanguages.
This is how Lisp has been written traditionally, as a language which one modifies to make their own specific DSL.
However, one would also wish to have a typed language when developing a large software system.

Choosing the type system, then, becomes the tricky part.
For example, a TCP/IP stack might want a dependent type system with linear types to be able to have provably high performance, while a user program might want TODO

OftLisp therefore supports *composable* type systems.
That is, in OftLisp, different modules within a single program can have different type systems.
