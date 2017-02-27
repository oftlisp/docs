# Name Mangling in OftLisp

OftLisp uses a system similar to C++, in which the components of module paths
are separated by numbers containing the length of the next segment. The Oftlisp
symbols also have a prefix of `oft` to distinguish them. The actual value is
separated from the module path by an underscore. Characters other than ASCII
alphanumerics are converted to escapes. An escape is of the format `_x??`, where
the two `?` characters are replaced with the lowercase hex value of the byte
being escaped. Escaped characters that are not part of ASCII are converted to
UTF-8, and their bytes are then escaped.

For example the name `foo` in the package `example.com/bar` would be mangled to:

`oft14example_x2ecom3bar_foo`

Compiler intrinisics and the runtime may have any name that does not fit this
scheme, or can have a name such that they're stored in the `std/internal`
module. The former is more appropriate for the runtime, while the latter is more
appropriate for compiler intrinsics.
