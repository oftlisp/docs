+++
title = "The Module System"
+++

## Packages and Modules

In OftLisp, code is organized into packages and modules.
Packages form the root of the import hierarchy, while the values form the leaves.

For example, in the import path `std/io/paths/join-paths`, the package is `std`, the module is `io/paths`, and the imported value is `join-paths`.

In OftLisp, each package is compiled as a single unit.
A consequence of this is that two *packages* cannot depend on each other circularly.
Two modules within the same package, however, may.

## `package.oftd`

At the root directory of each package, there is a file named `package.oftd`.
This file defines the package's name, version, and dependencies.
This file is documented on its own page.

## Package Organization

For a package named `foo`, an example directory structure might look like:

 - `foo`
   - `package.oftd`
   - `src`
     - `bar.oft`
	 - `baz/`
	   - `quux.oft`
	 - `baz.oft`
	 - `xyzzy.oft`

Each file corresponds to a module with a corresponding name.
For example, the module `foo/bar` corresponds to the file `foo/src/bar.oft`.

Assuming that `package.oftd` contains a `default-module` of `baz/quux`, imports from `foo` will resolve to `foo/baz/quux`.
In this case, `foo/src/baz/quux.oft` should still be declared as `foo/baz/quux`.

## Module Organization

A module starts with a `module` statement, giving the module's name and its exported values.
Next follow a series of `import` statements, giving the module name and the values to import.

In either case, the list may be replaced with the symbol `*`, meaning export all or import all.

For example:

```oftlisp
(module foo/bar
  (say-hello))

(import foo/baz
  (say-a-thing))
(import foo/xyzzy *)

(defn say-hello ()
  ; get-message is imported from foo/xyzzy.
  (say-a-thing (get-message 'hello)))
```
