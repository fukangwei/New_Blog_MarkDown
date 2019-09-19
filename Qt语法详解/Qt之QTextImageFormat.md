---
title: Qt之QTextImageFormat
date: 2019-01-02 15:47:09
categories: Qt语法详解
---
&emsp;&emsp;The `QTextImageFormat` class provides formatting information for images in a `QTextDocument`.

Header             | Inherits
-------------------|-----------------
`QTextImageFormat` | `QTextCharFormat`

**Note**: All functions in this class are reentrant.

### Public Functions

Return    | Function
----------|--------
          | `QTextImageFormat()`
`qreal`   | `height() const`
`bool`    | `isValid() const`
`QString` | `name() const`
`void`    | `setHeight(qreal height)`
`void`    | `setName(const QString & name)`
`void`    | `setWidth(qreal width)`
`qreal`   | `width() const`

### Detailed Description

&emsp;&emsp;The `QTextImageFormat` class provides formatting information for images in a `QTextDocument`.
&emsp;&emsp;Inline images are represented by an object replacement character (`0xFFFC` in `Unicode`) which has an associated `QTextImageFormat`. The image format specifies a name with `setName()` that is used to locate the image. The size of the rectangle that the image will occupy is specified using `setWidth()` and `setHeight()`.
&emsp;&emsp;Images can be supplied in any format for which `Qt` has an image reader, so `SVG` drawings can be included alongside `PNG`, `TIFF` and other bitmap formats.

### Member Function Documentation

- `QTextImageFormat::QTextImageFormat()`: Creates a new image format object.
- `qreal QTextImageFormat::height() const`: Returns the `height` of the rectangle occupied by the image.
- `bool QTextImageFormat::isValid() const`: Returns `true` if this image format is `valid`; otherwise returns `false`.
- `QString QTextImageFormat::name() const`: Returns the name of the image. The name refers to an entry in the application's resources file.
- `void QTextImageFormat::setHeight(qreal height)`: Sets the `height` of the rectangle occupied by the image.
- `void QTextImageFormat::setName(const QString & name)`: Sets the `name` of the image. The `name` is used to locate the image in the application's resources.
- `void QTextImageFormat::setWidth(qreal width)`: Sets the `width` of the rectangle occupied by the image.
- `qreal QTextImageFormat::width() const`: Returns the width of the rectangle occupied by the image.