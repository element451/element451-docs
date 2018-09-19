# Formulas for Import/Export

* [Examples](#examples)
* [Logic Functions (IF)](#logic-functions)
* [Strings](#string-operations)
* [Regex Operations](#regex-operations)
* [Maps and Lists](#maps-and-lists)
* [Dates](#date-functions)
* [Math Functions](#math-functions)
 

# Examples 

Add default value to a field for all records. Use double quotes for the value.
```
Date: DATE_DEFAULT("2017-12-06")
String: "USA"
```

Map Majors with a Data Source for Majors and assign "Undecided" major if no value:
```
DS_MAP([C36],"drew.data_source.majors","name","guid","drew.majors.31")
```

Zip Code Truncating 0’s
```
PAD([C16], 0, 5)
```

CEEB Code Truncating 0’s but also no value if all 0’s
```
REGEXP_REPLACE(PAD([C14] , 0 , 6), "000000", "")
```

Add Fall to a Graduation Year to create Intended Term Fall 2018
```
DS_MAP(CONCAT("Fall ",[C14]),"drew.data_source.610","label","name"," ")
```

Assign Source Code based on input.
```
IF([C1] = "Prospect Card", "drew.taxonomy.717", "drew.taxonomy.721")
```

Validate Phone Number - only numbers
```
TRIM(IF(STRLEN(RIGHT(REGEXP_REPLACE([C4], "[-,a-z,@,.,()]", TRIM(" ")),10))<10," ",RIGHT(REGEXP_REPLACE([C4], "[-,a-z,@,.,()]", TRIM(" ")),10)))
```

# Logic Functions

## IF

The `IF(b, case1, case2)` function provides branching capability. If `B` is not `0`, then it returns `case1`, else it returns `case2`. Behavior is similar to PHP's: `return b ? case1 : case2;`. If `b==0` then `case1` will not be Evaluated, and vice versa.

Usage example:

```
Name of the function: IF
Number of parameters: 3
Type of parameters: bool, any, any

INPUT: IF(HEIGHT, 3/HEIGHT, 3)
DESCRIPTION: Will make sure 3/HEIGHT does not cause division by zero
```

# String Operations

## STRLEN

The string lenght function returns the length of the string parameter.

Usage example:

```
Name of the function: STRLEN
Number of parameters: 1
Type of parameters: number

INPUT: STRLEN("ABC")
OUTPUT: 3
```

## STR

The string function return the string representation of the passed value.

Usage example:

```
Name of the function: STR
Number of parameters: 1
Type of parameters: number

INPUT: STR(451)
OUTPUT: "451"
```

## SUBSTR

The substring function return part of a string. The first parameter is the input string. Must be one character or longer. The second parameter is the start point. And the third parameter represent the lenght.

### 2nd param - start

If `start` is non-negative, the returned string will start at the `start`'th position in `string`, counting from zero. For instance, in the string `'abcdef'`, the character at position `0` is `'a'`, the character at position `2` is `'c'`, and so forth.

If `start` is negative, the returned string will start at the `start`'th character from the end of `string`.

If `string` is less than `start` characters long, `FALSE` will be returned.

### 3rd param - lenght

If `length` is given and is positive, the string returned will contain at most `length` characters beginning from `start` (depending on the length of `string`).

If `length` is given and is negative, then that many characters will be omitted from the end of `string` (after the start position has been calculated when a `start` is negative). If `start` denotes the position of this truncation or beyond, `FALSE` will be returned.

If `length` is given and is `0`, `FALSE` or `NULL`, an empty string will be returned.

If `length` is omitted, the substring starting from `start` until the end of the string will be returned.

Usage example:

```
Name of the function: SUBSTR
Number of parameters: 3
Type of parameters: string, number, number

INPUT: SUBSTR("abcdef", 0, -1)
OUTPUT: "abcde"

INPUT: SUBSTR("abcdef", 2, -1)
OUTPUT: "cde"

INPUT: SUBSTR("abcdef", 4, -4)
OUTPUT: FALSE

INPUT: SUBSTR("abcdef", -3, -1)
OUTPUT: "de"
```

## CONCAT

The concatenation function `CONCAT` returns the concatanated strings. There is no preset limit on the number of parameters.

Usage example:

```
Name of the function: CONCAT
Number of parameters: unlimited
Type of parameters: number

INPUT: CONCAT("ABC", "DEF")
OUTPUT: "ABCDEF"
```

## TRIM

The trim function `TRIM` returns the trimmed version of the given string.

Usage example:

```
Name of the function: TRIM
Number of parameters: 1
Type of parameters: number

INPUT: TRIM(" abc  ")
OUTPUT: "abc"
```

## RTRIM

The `RTRIM` remove whitespaces from the ends of the string.

Usage example:

```
Name of the function: RTRIM
Number of parameters: 1
Type of parameters: number

INPUT: RTRIM("abc  ")
OUTPUT: "abc"

INPUT: RTRIM("  abc ")
OUTPUT: "  abc"
```

## LTRIM

The `LTRIM` remove whitespaces from the starts of the string.

Usage example:

```
Name of the function: LTRIM
Number of parameters: 1
Type of parameters: number

INPUT: LTRIM("  abc")
OUTPUT: "abc"

INPUT: LTRIM(" abc  ")
OUTPUT: "abc  "

```

## CHR

The `CHR` functions gets a character with the given `ASCII` code.

Usage example:

```
Name of the function: CHR
Number of parameters: 1
Type of parameters: number

INPUT: CHR(65)
OUTPUT: "A"
```

## UPPER

The uppercase function `UPPER` will capitalize all letters of string.

Usage example:

```
Name of the function: UPPER
Number of parameters: 1
Type of parameters: string

INPUT: UPPER("hello world")
OUTPUT: "HELLO WORLD"
```

## LOWER

The lowercase function `LOWER` will lowercase all letters of string.

Usage example:

```
Name of the function: LOWER
Number of parameters: 1
Type of parameters: string

INPUT: LOWER("HELLO WORLD")
OUTPUT: "hello world"
```

## TITLECASE

The titlecase function `TITLECASE` will capitalize first letter of each word.

Usage example:

```
Name of the function: TITLECASE
Number of parameters: 1
Type of parameters: string

INPUT: TITLECASE("heLlO woRLd")
OUTPUT: "Hello World"
```

## MATCH

The match function `MATCH` check if strings are equal. If `caseSensitive` is `TRUE`, then do not ignore case.

Usage example:

```
Name of the function: MATCH
Number of parameters: 2
Type of parameters: p1, p2, caseSensitve = 1

INPUT: MATCH("Hello", "hello", FALSE)
OUTPUT: TRUE

INPUT: MATCH("Hello", "hello", TRUE)
OUTPUT: FALSE

INPUT: MATCH ("Hello", "Hello", TRUE)
OUTPUT: TRUE
```

## PAD

The pad function `PAD` will pads a given string to given length by given char.

* If `length` is `< 0`, pad the `string` from the right instead.
* If `length` is `< length of string`, return `string`.

Usage example:

```
Name of the function: PAD
Number of parameters: 3
Type of parameters: string, char, length

INPUT: PAD("137", '0', 5)
OUTPUT: "00137"

INPUT: PAD("137", '2', -6)
OUTPUT: "137222"

INPUT: PAD("137", '0', 2)
OUTPUT: "137"
```

## CONTAINS

Check if string contains substring.

Usage example:

```
Name of the function: CONTAINS
Number of parameters: 2
Type of parameters: string, substr

INPUT: CONTAINS("Hello World", "Hello")
OUTPUT: TRUE

INPUT: CONTAINS("Hello World", "random string")
OUTPUT: FALSE
```

## HAS_ONE_TRUE

Returns TRUE if an option in options has `attribute = TRUE`. Otherwise, returns `FALSE`.

Usage example:

```
Name of the function: HAS_ONE_TRUE
Number of parameters: 2
Type of parameters: json_input, attribute

INPUT: HAS_ONE_TRUE("[{'associated_degree':false},{'associated_degree':true,'text':'European'}]", "associated_degree")
OUTPUT: TRUE
```

## STARTS_WITH

Check if `string` starts with `substr`.

Usage example:

```
Name of the function: STARTS_WITH
Number of parameters: 2
Type of parameters: string, substr

INPUT: STARTS_WITH("Hello World", "Hello")
OUTPUT: TRUE
```

## ENDS_WITH

Check if string ends with substring.

Usage example:

```
Name of the function: ENDS_WITH
Number of parameters: 2
Type of parameters: string, substr

INPUT: ENDS_WITH("Hello World", "World")
OUTPUT: TRUE
```

## LEFT

Get the first `numChars` characters of `string`.

Usage example:

```
Name of the function: LEFT
Number of parameters: 2
Type of parameters: string, numChars

INPUT: LEFT("Hello World", 5)
OUTPUT: "Hello"
```

## RIGHT

Get the last `numChars` characters of `string`.

Usage example:

```
Name of the function: RIGHT
Number of parameters: 2
Type of parameters: string, numChars

INPUT: RIGHT("Hello World", 5)
OUTPUT: "World"
```

## LEN

Get the number of characters in `string`.

Usage example:

```
Name of the function: LEN
Number of parameters: 1
Type of parameters: string

INPUT: LEN("Hello World")
OUTPUT: 11
```

## SPLIT_INDEX

Split `string` by delimiter and get the index substring.

Usage example:

```
Name of the function: SPLIT_INDEX
Number of parameters: 3
Type of parameters: string, delim, index

INPUT: SPLIT_INDEX("Hello World", " ", 2)
OUTPUT: "World"
```


# Regex Operations

## REGEXP_MATCH

Match `string` with `pattern`. Uses PHP `RegExp`.

Usage example:

```
Name of the function: REGEXP_MATCH
Number of parameters: 2
Type of parameters: string, pattern

INPUT: REGEXP_MATCH("Hello World", "H[a-z]{4}\s.*")
OUTPUT: TRUE
```

## REGEXP_REPLACE

Replace `pattern` in `string` with `replaceWith`. Uses PHP `RegExp`.

Usage example:

```
Name of the function: REGEXP_REPLACE
Number of parameters: 3
Type of parameters: string, pattern, replaceWith

INPUT: REGEXP_REPLACE("Hello World", "[a-z]", "x")
OUTPUT: "Hxxxx Wxxxx"
```

## REGEXP_EXTRACT

Get substring within `string` that matches the first parenthetical expression of `pattern`. Uses PHP `RegExp`.

Usage example:

```
Name of the function: REGEXP_EXTRACT
Number of parameters: 2
Type of parameters: string, pattern

INPUT: REGEXP_EXTRACT("Hello World", "Hello\s(.*)")
OUTPUT: "World"
```

## REGEXP_EXTRACT_NTH

Get substring within `string` that matches the index parenthetical expression of `pattern`. Uses PHP `RegExp`.

Usage example:

```
Name of the function: REGEXP_EXTRACT_NTH
Number of parameters: 3
Type of parameters: string, pattern, index

INPUT: REGEXP_EXTRACT_NTH("Hello World", "H([a-z]+)\s(.*)", 1)
OUTPUT: "ello"
```

# Maps and Lists

## DS_MAP

Luminous Data Source mapping. The `output` value is the value of the output key for the setting matched by match in the `dataSource`. If no settings are matched with `match`, default is returned.
Checks the luminous_data_sources collection for item `dataSource`, check its options, finds an item which `match` field is `input`, and returns it's `name` field.

Usage example:

```
Name of the function: DS_MAP
Number of parameters: 5
Type of parameters: input, dataSource, match, output, default

INPUT: DS_MAP("cnr.terms.1", "data_source.terms", "guid", "name", "TermNotFound")
OUTPUT: "Fall 2016"

INPUT: DS_MAP("cnr.terms.1350", "data_source.terms", "guid", "name", "TermNotFound")
OUTPUT: "TermNotFound"

INPUT: DS_MAP("USA", "data_source.country", "alpha3", "name", "CountryNotFound")
OUTPUT: "United States"
```

## CEEB_MAP

Same as `DS_MAP`, but the Data Source is the `luminous_ceebs` collection.

Usage example:

```
Name of the function: CEEB_MAP
Number of parameters: 4
Type of parameters: input, match, output, default

INPUT: CEEB_MAP(181615,"ceeb","name", "CeebNotFound")
OUTPUT: "Academy At Shawnee"
```

## CHECKBOX_FIRST

Returns the "text" field of the first checkbox that has "checked"=TRUE.

Usage example:

```
Name of the function: CHECKBOX_FIRST
Number of parameters: 1
Type of parameters: json_input

INPUT: CHECKBOX_FIRST("[{'checked':true,'text':'Asian'},{'checked':false,'text':'European'},{'checked':true,'text':'American'}]")
OUTPUT: "Asian"

INPUT: CHECKBOX_FIRST("[{'checked':false,'text':'Asian'},{'checked':false,'text':'European'},{'checked':false,'text':'American'}]")
OUTPUT: ""
```

## CHECKBOX_CHECKED

Used mostly internally, for exporting checked values of a checkbox field. checkboxes will most likely be a slug. The internal object is expected to have fields "checked" and "text". If "checked" is TRUE, then "text" will be output.

Usage example:

```
Name of the function: CHECKBOX_CHECKED
Number of parameters: 2
Type of parameters: json_input, delimiter

INPUT: CHECKBOX_CHECKED("[{'checked':true,'text':'Asian'},{'checked':false,'text':'European'},{'checked':true,'text':'American'}]", ",")
OUTPUT: "Asian,American"

INPUT: CHECKBOX_CHECKED("[{'checked':false,'text':'Asian'},{'checked':false,'text':'European'},{'checked':false,'text':'American'}]", ",")
OUTPUT: ""
```

## TAXONOMY_MAP

Math function to fetch taxonomy.

Usage example:

```
Name of the function: TAXONOMY_MAP
Number of parameters: 4
Type of parameters: input, match, output, default

INPUT: TAXONOMY_MAP("cnr.taxonomy.18", "guid", "value", "MISSING")
OUTPUT: "grad-admit"

INPUT: TAXONOMY_MAP("cnr.taxonomy.18", "guid", "name", "MISSING")
OUTPUT: "Grad Admits" 

INPUT: TAXONOMY_MAP("", "guid", "name", "MISSING")
OUTPUT: "MISSING" 

INPUT: TAXONOMY_MAP("grad-admit", "value", "name", "MISSING")
OUTPUT: "Grad Admits"

INPUT: TAXONOMY_MAP("grad-admit", "value", "guid", "MISSING")
OUTPUT: "cnr.taxonomy.6"
```

## SC_MAP_IMPORT

Math function used to import taxonomies to user. Use with `[user-sources-source-code]` and `[user-sources-source-code-segment]` user fields. This function is an alias for `TAXONOMY_MAP` default of match and output as value/guid. Note: You can use the missing value to figure out what can not be found, eg. `SC_MAP_IMPORT([C1], CONCAT("Cant find mapping for: ",[C1]) )`

```
Name of the function: SC_MAP_IMPORT
Number of parameters: 2
Type of parameters: input, default

INPUT: SC_MAP_IMPORT([c42], "MISSING") - where c42 is the 42th imported column
ALIAS FOR: TAXONOMY_MAP([c42], "value", "guid", "MISSING")
OUTPUT: "tu.taxonomy.1"
```

## SC_MAP_EXPORT

Math function used to export taxonomies from user. Use with `[user-sources-source-code]` and `[user-sources-source-code-segment]` user fields. This function is an alias for `TAXONOMY_MAP` default of match and output as guid/value.

```
Name of the function: SC_MAP_EXPORT
Number of parameters: 2
Type of parameters: input, default

INPUT: SC_MAP_EXPORT([user-custom-source])
ALIAS FOR: TAXONOMY_MAP([user-custom-source], "guid", "value", "MISSING")
OUTPUT: "grad-admit"
```

# Date Functions 

## DATE_ADD

Add interval `datePart` to `date`. `date` should be in `Y-m-d H:i:s` format, `interval` is an `integer`, `datePart` is `second/minute/hour/day/month/year`. Keeps output as `Y-m-d H:i:s`.

Usage example:

```
Name of the function: DATE_ADD
Number of parameters: 3
Type of parameters: datePart, interval, date

INPUT: DATE_ADD("month", 5, "2016-10-04 12:45:23")
OUTPUT: "2017-03-04 12:45:23"
```

## DATE_DIFF

Get the difference between `endDate` and `startDate` in `datePart`. Using datePart as unit (which is one of `second/minute/hour/day/month/year`).

Usage example:

```
Name of the function: DATE_DIFF
Number of parameters: 3
Type of parameters: datePart, startDate, endDate

INPUT: DATE_DIFF("month", "2016-10-04 12:45:23", "2017-03-04 12:45:23")
OUTPUT: 5
```

## DATE_COMPARE

Check if `p1 operator p2` is `TRUE`. `p1`, `p2` is `Y-m-d H:i:s` date, `operator` is `<;>;<=;>=;!=;=`.

Usage example:

```
Name of the function: DATE_COMPARE
Number of parameters: 3
Type of parameters: p1, operator, p2

INPUT: DATE_COMPARE("2016-10-04 12:45:23", "=", "2016-10-04 12:45:23")
OUTPUT: TRUE
```

## DATE_FORMAT

Reformat `input` date for `format`, which should be a valid PHP `format` string.

Usage example:

```
Name of the function: DATE_FORMAT
Number of parameters: 2
Type of parameters: input, format

INPUT: DATE_FORMAT("2016-10-04 12:45:23", "D M d H:i:s O Y")
OUTPUT: Tue Oct 04 12:45:23 +0000 2016
```

## DATE_DEFAULT

Reformat current date for format to the default format.

Usage example:

```
Name of the function: DATE_DEFAULT
Number of parameters: 1
Type of parameters: format

INPUT: DATE_DEFAULT("D M d H:i:s O Y")
OUTPUT: Fri Nov 17 09:29:26 -0500 2017
```

## DATE_READ

Reads an arbitrary formatted date, and converts it to the default format

Usage example:

```
Name of the function: DATE_READ
Number of parameters: 2
Type of parameters: datestring, format

INPUT: DATE_READ("01/02/03","d/m/y")
OUTPUT: 2003-02-01 00:00:00
```


# Math Functions

## SQR

A square of `X` is a number `R` such that `R=X^2`.

Usage example:

```
Name of the function: SQR
Number of parameters: 1
Type of parameters: number

INPUT: SQR(451)
OUTPUT: 203401
```

## SIN

The sine function `SIN(X)`. `X` is a real-type expression. `SIN` returns the sine of the angle `X` in radians.

Usage example:

```
Name of the function: SIN
Number of parameters: 1
Type of parameters: number

INPUT: SIN(1)
OUTPUT: 0.84{...}
```

## COS

The cosine function `COS(X)`. `X` is a real-type expression. `COS` returns the cosine of the angle `X` in radians.

Usage example:

```
Name of the function: COS
Number of parameters: 1
Type of parameters: number

INPUT: COS(1)
OUTPUT: 0.54{...}
```


## ATAN

The inverse tangent function `ATAN(X)`.

Usage example:

```
Name of the function: ATAN
Number of parameters: 1
Type of parameters: number

INPUT: ATAN(1)
OUTPUT: 0.79{...}
```

## SINH

The hyperbolic sine function `SINH(X)`.

Usage example:

```
Name of the function: SINH
Number of parameters: 1
Type of parameters: number

INPUT: SINH(1)
OUTPUT: 1.18{...}
```

## COSH

The hyperbolic cosine function `COSH(X)`.

Usage example:

```
Name of the function: COSH
Number of parameters: 1
Type of parameters: number

INPUT: COSH(1)
OUTPUT: 1.54{...}
```

## COTAN

The cotangent function `COTAN(X)`

Usage example:

```
Name of the function: COTAN
Number of parameters: 1
Type of parameters: number

INPUT: COTAN(1)
OUTPUT: 0.64{...}
```

## TAN

The tangent function `TAN(X)`.

Usage example:

```
Name of the function: TAN
Number of parameters: 1
Type of parameters: number

INPUT: TAN(1)
OUTPUT: 1.56{...}
```

## EXP

The exponential function `EXP(X)`.

Usage example:

```
Name of the function: EXP
Number of parameters: 1
Type of parameters: number

INPUT: EXP(1)
OUTPUT: 2.72{...}
```

## LN

The natural logarithm `LN(X)` is the logarithm having base `e`, where `e=2.718281828...`.

Usage example:

```
Name of the function: LN
Number of parameters: 1
Type of parameters: number

INPUT: LN(451)
OUTPUT: 6.11{...}
```

## LOG

Gives the natural logarithm of `X` (logarithm to `base 10`).

Usage example:

```
Name of the function: LOG
Number of parameters: 1
Type of parameters: number

INPUT: LOG(451)
OUTPUT: 2.65{...}
```

## SQRT

A square root of `X` is a number `R` such that `R^2=X`.

Usage example:

```
Name of the function: SQRT
Number of parameters: 1
Type of parameters: number

INPUT: SQRT(203401)
OUTPUT: 451
```

## ABS

The absolute value of a `real number` `X` is denoted `ABS(X)` and defined as the "unsigned" portion of `X`.

Usage example:

```
Name of the function: ABS
Number of parameters: 1
Type of parameters: number

INPUT: ABS(-451)
OUTPUT: 451
```

## SIGN

The sign of a `real number`, also called sgn or signum, is `-1` for a `negative` number (i.e., one with a `minus sign` `"-"`), `0` for the number `zero`, or `+1` for a `positive` number (i.e., one with a `plus sign` `"+"`).

Usage example:

```
Name of the function: SINH
Number of parameters: 1
Type of parameters: number

INPUT: SIGN(451)
OUTPUT: 1

INPUT: SIGN(-451)
OUTPUT: -1
```

## TRUNC

To truncate a `real number` is to discard its noninteger part. Truncation of a (positive) number `X` therefore corresponds to taking the `floor function` `FLOOR`.

Usage example:

```
Name of the function: TRUNC
Number of parameters: 1
Type of parameters: number

INPUT: TRUNC(-3.2)
OUTPUT: -3

INPUT: TRUNC(3.2)
OUTPUT: 3
```

## CEIL

The ceiling function `CEIL` gives the smallest `integer` `>=X`.

Usage example:

```
Name of the function: CEIL
Number of parameters: 1
Type of parameters: number

INPUT: CEIL(-3.2)
OUTPUT: -3

INPUT: CEIL(3.2)
OUTPUT: 4
```

## FLOOR

The floor function `FLOOR`, also called the greatest integer function or integer value, gives the largest `integer` less than or equal to `X`.

Usage example:

```
Name of the function: FLOOR
Number of parameters: 1
Type of parameters: number

INPUT: FLOOR(-3.2)
OUTPUT: -4

INPUT: FLOOR(3.2)
OUTPUT: 3
```

## RND

The random function `RND` generates a random number (double value) between 0 and 1.

Usage example:

```
Name of the function: RND
Number of parameters: 0
Type of parameters: number

INPUT: RND()
OUTPUT: 0.3{...}
```

## VAL

The value function `VAL` returns the floating point numeric value of the string argument.

Usage example:

```
Name of the function: VAL
Number of parameters: 1
Type of parameters: number

INPUT: VAL("3.1")
OUTPUT: 3.1
```

## POW

ThepPower function `POW` raises Base to any power. For fractional exponents or exponents greater than MaxInt, Base must be greater than 0.

Usage example:

```
Name of the function: POW
Number of parameters: 2
Type of parameters: number, number

INPUT: POW(451, 2)
OUTPUT: 203401
```

## LOGN

The LogN function `LOGN` returns the log base N of X.

Usage example:

```
Name of the function: LOGN
Number of parameters: 2
Type of parameters: number, number

INPUT: LOGN(10, 100)
OUTPUT: 2
```

## MIN

The minimum function `MIN` gives the smallest value of a set.

Usage example:

```
Name of the function: MIN
Number of parameters: 2
Type of parameters: number

INPUT: MIN(2, 3)
OUTPUT: 2
```

## MAX

The maximum function `MAX` gives the largest value of a set.

Usage example:

```
Name of the function: MAX
Number of parameters: 2
Type of parameters: number

INPUT: MAX(2, 3)
OUTPUT: 3
```

## MOD

The mod function `MOD` gives the remainder on division of M by N.

Usage example:

```
Name of the function: MOD
Number of parameters: 2
Type of parameters: number, number

INPUT: MOD(451, 2)
OUTPUT: 1
```

## NUM

To number function tries to convert an expression to a `number`.

Usage example:

```
Name of the function: NUM
Number of parameters: 1
Type of parameters: number

INPUT: NUM("$100.00")
OUTPUT: 100.00
```

## SUM

A sum is the result of an `addition`. There is no preset limit on the number of parameters.

Usage example:

```
Name of the function: SUM
Number of parameters: 1
Type of parameters: number

INPUT: SUM(1, 2, 3, 4)
OUTPUT: 10
```
