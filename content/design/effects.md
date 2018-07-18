+++
title = "Effect Systems"
+++

OftLisp is a strict language, with side effects.
Nonetheless, most effects are not explicitly present.
The main examples are errors, (mutable) state, and nondeterminism.
The traditional (in Haskell, at least) approach to modelling these effects (possibly in composition) is that of monads and monad transformer stacks.
However, in a dynamically typed setting, monad transformer stacks are awkward to use -- since one doesn't have types to help dispatch the `lift` function, one must have, for example, a `lift-error-through-state-to-nondet` function.
There are alternate approaches, including Kleisli Functors and Algebraic Effects.

## Monads

Benefits:
 - Familiar `do`-notation

Disadvantages:
 - Harder to explain than "composition of functions with effects"
  - (Actually, could probably teach with Kleisli composition first, then monad bind)

## Algebraic Effects

Benefits:
 - Cleaner than a monad transformer stack
 - Looks actually possible to implement in a dynamic setting...

Mixed?
 - Compiles to CPS

Disadvantages:
 - Requires a base monad

## Kleisli Functors

Benefits:
 - Can abstract inner effect representation completely
   - Instead of `(a -> State b)`, can write `(StateKA a b)`
 - Simpler laws, rewrite process is fairly intuitive
 - Models a category more-or-less explicitly

Disadvantages:
 - `kmap` still needs to be created for transformer stacks
 - `do`-notation becomes a lot more complex
