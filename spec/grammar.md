# Formal Grammar

OftLisp's grammar is described by the following:

```ebnf
program = expr-list
expr-list = opt-space expr expr-list
          | opt-space
opt-space = (any Unicode character with the White_Space property) opt-space
          | ε
expr = s-expr
     | list
     | number
     | symbol
     | string
     | reader-macro opt-space expr
s-expr = "(" expr-list ")"
list = "[" expr-list "]"
number = machine-number
       | opt-sign unsigned-number
machine-number = "0x" hex-digit-list
               | "0o" oct-digit-list
               | "0b" bin-digit-list
hex-digit-list = hex-digit hex-digit-list | ε
hex-digit = digit | "a" | "A" | "b" | "B" | "c" | "C" | "d" | "D" | "e" | "E" | "f" | "F"
oct-digit-list = oct-digit oct-digit-list | ε
oct-digit = "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7"
bin-digit-list = bin-digit bin-digit-list | ε
bin-digit = "0" | "1"
opt-sign = sign | ε
sign = "+" | "-"
unsigned-number = real | complex
real = integer
     | rational
     | floating
integer = digit integer
        | digit
digit = "0" | "1" | "2" | "3" | "4" | "5" | "6" | "7" | "8" | "9"
rational = integer "/" integer
floating = integer "." integer
complex = real sign real "i"
symbol = symbol-start-char symbol-char-list
symbol-char-list = symbol-char symbol-char-list
                 | ε
symbol-char = symbol-start-char | digit
symbol-start-char = TODO
string = '"' string-char-list '"'
string-char-list = string-char string-char-list | ε
string-char = (any Unicode character but "\" and '"')
            | "\" string-escape
string-escape = "n"
              | "r"
              | "t"
              | "u" hex-digit hex-digit hex-digit hex-digit
              | "U" hex-digit hex-digit hex-digit hex-digit hex-digit hex-digit hex-digit hex-digit
              | "x" hex-digit hex-digit
reader-macro = "'"
             | "`"
             | ","
             | ",@"
```
