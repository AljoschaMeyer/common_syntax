# Generic Syntax

A collection of common sytax elements for computer languages, such as identifiers, integer literals, floating-point literals, string literals and so on. These definitions are placed in the public domain and are intended to be used in different computer languages.

All input text should be valid UTF-8.

## Whitespace

The bytes `0x09` (tab), `0x0a` (newline), `0x0d` (carriage return), and `0x20` (space) are considered *whitespace*. A sequence of valid UTF-8 beginning with `#` and ending with either `0x0a` (newline) or the end of input is considered *whitespace* as well, called a (line) comment.

Whitespace should generally have no impact on a parsing result.

## Integer Literal

An **integer literal** is either a *decimal integer literal*, a *hexadecimal integer literal* or a *binary integer literal*. A language using integer literals should specify a range of valid numbers, rejecting literals that encode an integer that is too small or too large.

### Decimal Integer Literal

A **decimal integer literal** consists of an optional sign (`-` or `+`), followed by any number of decimal digits (`0-9`). Every digit may be followed by an arbitrary number of underscores (`_`), which are ignored when decoding.

```
4__2_  # 42
+42    # 42
-42___ # -42
```

### Hexadecimal Integer Literal

A **hexadecimal integer literal** begins with `0x`, followed by any number of hexadecimal digits (`0 - 9`, `A-F`, and `a-f`). Every digit may be followed by an arbitrary number of underscores (`_`), which are ignored when decoding. Hexadecimal integer literals can only encode nonnegative integers.

```
0xaA   # 170
0x5_e_ # 94
```

### Binary Integer Literal

A **binary integer literal** begins with `0b`, followed by any number of Binary digits (`0` or `1`). Every digit may be followed by an arbitrary number of underscores (`_`), which are ignored when decoding. Binary integer literals can only encode nonnegative integers.

```
0b10      # 2
0b10__0_1 # 9
```

## Floating-Point Literal

Positive infinity is either encoded as `Inf` or as `+Inf`. Negative infinity is encoded as `-Inf`. NaN is encoded as `NaN` and should be decoded to the NaN whose bits are all 1. No other NaNs can be explicitly represented.

Encodings for all other floating-point numbers consist of an optional sign (`-` or `+`), followed by one or more decimal digits (`0-9`), followed by a `.`, followed by one or more decimal digits, optionally followed by an exponent: `e` or `E`, an optional sign (`+` or `-`), followed by one or more decimal digits. Every digit in the literal may be followed by an arbitrary number of underscores (`_`), which are ignored when decoding.

When decoding a float, the input may not be representable exactly. In these cases, use ["round to nearest, ties to even"](https://en.wikipedia.org/wiki/IEEE_754#Roundings_to_nearest) rounding mode. As a consequence, floats do not need to be encoded as their exact decimal representation, implementations may instead use a smaller decimal representation that still rounds to the correct float. Note that the rounding may result in an infinity, so it is possible to encode infinities as large decimal numbers (e.g. `9999.9e999999`) rather than `Inf`.

```
0.0
-0_.0_e+2__
# neither of the following is a floating-point literal:
# 0
# 0.
# .0
```

## Byte String

A **byte string** is a sequence of arbitrary bytes. A **byte string literal** is either an *integer byte string literal*, a *hexadecimal byte string literal* or a *binary byte string literal*. A language using byte string literals should specify a maximum length for them and reject all byte string literals that decode to byte strings of greater length.

### Integer Byte String Literal

An **integer byte string literal** is a comma-separated (`,`) list of bytes, enclosed between `@[` and `]`. The bytes are encoded as *integer literals* between `0` and `255` inclusive. An optional trailing comma before the closing bracket is allowed. Any amount of whitespace can be placed between brackets, contained values, and commas.

```
@[0, 128, 64]
@[
    243, 0b1_0,0xff_  ]
@[,]
```

### Hexadecimal Byte String Literal

A **hexadecimal byte string literal** begins with `@x[`, followed by an even number of hexadecimal digits (`0 - 9`, `A-F`, and `a-f`), followed by `]`. Every digit may be followed by an arbitrary number of underscores (`_`), which are ignored when decoding.

```
@x[4A2____b]
```

### Binary Byte String Literal

A **binary byte string literal** begins with `@b[`, followed by an even number of binary digits (`0` or `1`), followed by `]`. Every digit may be followed by an arbitrary number of underscores (`_`), which are ignored when decoding.

```
@x[1010_0001__11111111]
```

## UTF-8 String

A **UTF-8 string** is a sequence of bytes that form valid [UTF-8](https://en.wikipedia.org/wiki/UTF-8). A **UTF-8 string literal** is either an *escaping UTF-8 string literal*, or a *raw UTF-8 string literal*. A language using UTF-8 string literals should specify a maximum length for them and reject all UTF-8 string literals that decode to UTF-8 strings of greater length (measured in the number of UTF-8 bytes).

### Escaping UTF-8 String Literal

A **escaping UTF-8 string literal** begins with `"`, followed by up to `(2^63) - 1` bytes worth of scalar encodings (see next paragraph), followed by another `"`.

Each [unicode scalar](http://www.unicode.org/glossary/#unicode_scalar_value) can either be encoded literally or through an escape sequence. The literal encoding can be used for all scalar values other than `"` and `\` and consists of the utf-8 encoding of the scalar value. Alternatively, any of the following escape sequences can be used:

- `\"` for the character `"`
- `\\` for the character `\`
- `\t` for the character `horizontal tab` (`0x09`)
- `\n` for the character `new line` (`0x0a`)
- `\0` for the character `null` (`0x00`)
- `\{DIGITS}`, where `DIGITS` is the ASCII decimal representation of any scalar value. `DIGITS` must consist of one to six characters.

Unicode code points that are not unicode scalar values are disallowed, even when they form valid surrogate pairs.

```
"This is a UTF-8 string.
It can span multiple lines, and include escape sequences such as \", \\, \t, \n, \0, and \{324}."
```

### Raw UTF-8 String Literal

A **raw UTF-8 string literal** begins with one to 255 `@`, followed by `"`, followed by valid UTF-8, followed by `"`, followed by the same number of `@` as at the beginning of the string encoding.

```
@@@"This string can include unescaped '"'s without any parsing ambiguity.
Sequences of @@@ are also okay."@@@
```

## Name

A **name** consists of one to `2^63 - 1` characters from the following ranges: `0-9`, `A-Z`, `a-z`, `_`. The first character of a *name* must not be a decimal digit.

## Identifier

An **identifier** consists of one to `2^63 - 1` *names*, with two colons (`::`) between any two of them, e.g., `foo`, `foo::bar`, `foo::bar::baz`, and so on. Every *name* is also an *identifier*.
