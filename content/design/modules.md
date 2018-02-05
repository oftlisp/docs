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

## `package.TODO`

At the root directory of each package, there is a file named `package.TODO`.
This file defines the package's name, version, and dependencies.

TODO
