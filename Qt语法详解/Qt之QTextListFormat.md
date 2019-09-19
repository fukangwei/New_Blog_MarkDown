---
title: Qt之QTextListFormat
date: 2019-01-03 10:19:23
categories: Qt语法详解
---
&emsp;&emsp;The `QTextListFormat` class provides formatting information for lists in a `QTextDocument`.

Header            | Inherits
------------------|--------------
`QTextListFormat` | `QTextFormat`

**Note**: All functions in this class are reentrant.

### Public Types

- `enum`: Style { `ListDisc`, `ListCircle`, `ListSquare`, `ListDecimal`, ..., `ListUpperRoman` }

### Public Functions

Return    | Function
----------|-----------
          | `QTextListFormat()`
`int`     | `indent() const`
`bool`    | `isValid() const`
`QString` | `numberPrefix() const`
`QString` | `numberSuffix() const`
`void`    | `setIndent(int indentation)`
`void`    | `setNumberPrefix(const QString & numberPrefix)`
`void`    | `setNumberSuffix(const QString & numberSuffix)`
`void`    | `setStyle(Style style)`
`Style`   | `style() const`

### Detailed Description

&emsp;&emsp;The `QTextListFormat` class provides formatting information for lists in a `QTextDocument`.
&emsp;&emsp;A list is composed of one or more items, represented as text blocks. The list's format specifies the appearance of items in the list. In particular, it determines the indentation and the style of each item.
&emsp;&emsp;The indentation of the items is an integer value that causes each item to be offset from the left margin by a certain amount. This value is read with `indent()` and set with `setIndent()`.
&emsp;&emsp;The style used to decorate each item is set with `setStyle()` and can be read with the `style()` function. The style controls the type of bullet points and numbering scheme used for items in the list. Note that lists that use the decimal numbering scheme begin counting at `1` rather than `0`.
&emsp;&emsp;Style properties can be set to further configure the appearance of list items; for example, the `ListNumberPrefix` and `ListNumberSuffix` properties can be used to customize the numbers used in an ordered list so that they appear as `(1), (2), (3)`, etc.:

``` cpp
QTextListFormat listFormat;
​
listFormat.setStyle ( QTextListFormat::ListDecimal );
listFormat.setNumberPrefix ( "(" );
listFormat.setNumberSuffix ( ")" );
​
cursor.insertList ( listFormat );
```

### Member Type Documentation

&emsp;&emsp;`enum QTextListFormat::Style`: This enum describes the symbols used to decorate list items:

Constant                          | Value | Description
----------------------------------|-------|-------------
`QTextListFormat::ListDisc`       | `-1`  | a filled `circle`
`QTextListFormat::ListCircle`     | `-2`  | an empty `circle`
`QTextListFormat::ListSquare`     | `-3`  | a filled `square`
`QTextListFormat::ListDecimal`    | `-4`  | `decimal` values in ascending order
`QTextListFormat::ListLowerAlpha` | `-5`  | `lower` case Latin characters in `alphabetical` order
`QTextListFormat::ListUpperAlpha` | `-6`  | `upper` case Latin characters in `alphabetical` order
`QTextListFormat::ListLowerRoman` | `-7`  | `lower` case `roman` numerals (supports up to `4999` items only)
`QTextListFormat::ListUpperRoman` | `-8`  | `upper` case `roman` numerals (supports up to `4999` items only)

### Member Function Documentation

- `QTextListFormat::QTextListFormat()`: Constructs a new list format `object`.
- `int QTextListFormat::indent() const`: Returns the list format's `indentation`. The indentation is multiplied by the `QTextDocument::indentWidth` property to get the effective `indent` in pixels.
- `bool QTextListFormat::isValid() const`: Returns `true` if this list format is `valid`; otherwise returns `false`.
- `QString QTextListFormat::numberPrefix() const`: Returns the list format's number `prefix`.
- `QString QTextListFormat::numberSuffix() const`: Returns the list format's number `suffix`.
- `void QTextListFormat::setIndent(int indentation)`: Sets the list format's `indentation`. The indentation is multiplied by the `QTextDocument::indentWidth` property to get the effective `indent` in pixels.
- `void QTextListFormat::setNumberPrefix(const QString & numberPrefix)`: Sets the list format's number `prefix` to the string specified by `numberPrefix`. This can be used with all sorted list types. It does not have any effect on unsorted list types. The default prefix is an empty string.
- `void QTextListFormat::setNumberSuffix(const QString & numberSuffix)`: Sets the list format's number suffix to the string specified by `numberSuffix`. This can be used with all sorted list types. It does not have any effect on unsorted list types. The default suffix is `.`.
- `void QTextListFormat::setStyle(Style style)`: Sets the list format's `style`.
- `Style QTextListFormat::style() const`: Returns the list format's style.