---
title: Qt之QTextCharFormat
date: 2019-01-25 20:51:00
categories: Qt语法详解
---
&emsp;&emsp;The `QTextCharFormat` class provides formatting information for characters in a `QTextDocument`.

Header            | Inherits      | Inherited By
------------------|---------------|-----------------
`QTextCharFormat` | `QTextFormat` | `QTextImageFormat` and `QTextTableCellFormat`

**Note**: All functions in this class are reentrant.

### Public Functions

Return                     | Function
---------------------------|---------
                           | `QTextCharFormat()`
`QString`                  | `anchorHref() const`
`QStringList`              | `anchorNames() const`
`QFont`                    | `font() const`
`QFont::Capitalization`    | `fontCapitalization() const`
`QString`                  | `fontFamily() const`
`bool`                     | `fontFixedPitch() const`
`QFont::HintingPreference` | `fontHintingPreference() const`
`bool`                     | `fontItalic() const`
`bool`                     | `fontKerning() const`
`qreal`                    | `fontLetterSpacing() const`
`bool`                     | `fontOverline() const`
`qreal`                    | `fontPointSize() const`
`bool`                     | `fontStrikeOut() const`
`QFont::StyleHint`         | `fontStyleHint() const`
`QFont::StyleStrategy`     | `fontStyleStrategy() const`
`bool`                     | `fontUnderline() const`
`int`                      | `fontWeight() const`
`qreal`                    | `fontWordSpacing() const`
`bool`                     | `isAnchor() const`
`bool`                     | `isValid() const`
`void`                     | `setAnchor(bool anchor)`
`void`                     | `setAnchorHref(const QString & value)`
`void`                     | `setAnchorNames(const QStringList & names)`
`void`                     | `setFont(const QFont & font)`
`void`                     | `setFontCapitalization(QFont::Capitalization capitalization)`
`void`                     | `setFontFamily(const QString & family)`
`void`                     | `setFontFixedPitch(bool fixedPitch)`
`void`                     | `setFontHintingPreference(QFont::HintingPreference hintingPreference)`
`void`                     | `setFontItalic(bool italic)`
`void`                     | `setFontKerning(bool enable)`
`void`                     | `setFontLetterSpacing(qreal spacing)`
`void`                     | `setFontOverline(bool overline)`
`void`                     | `setFontPointSize(qreal size)`
`void`                     | `setFontStrikeOut(bool strikeOut)`
`void`                     | `setFontStyleHint(QFont::StyleHint hint, QFont::StyleStrategy strategy = QFont::PreferDefault)`
`void`                     | `setFontStyleStrategy(QFont::StyleStrategy strategy)`
`void`                     | `setFontUnderline(bool underline)`
`void`                     | `setFontWeight(int weight)`
`void`                     | `setFontWordSpacing(qreal spacing)`
`void`                     | `setTextOutline(const QPen & pen)`
`void`                     | `setToolTip(const QString & text)`
`void`                     | `setUnderlineColor(const QColor & color)`
`void`                     | `setUnderlineStyle(UnderlineStyle style)`
`void`                     | `setVerticalAlignment(VerticalAlignment alignment)`
`QPen`                     | `textOutline() const`
`QString`                  | `toolTip() const`
`QColor`                   | `underlineColor() const`
`UnderlineStyle`           | `underlineStyle() const`
`VerticalAlignment`        | `verticalAlignment() const`

### Detailed Description

&emsp;&emsp;The `QTextCharFormat` class provides formatting information for characters in a `QTextDocument`.
&emsp;&emsp;The character format of text in a document specifies the visual properties of the text, as well as information about its role in a hypertext document.
&emsp;&emsp;The font used can be set by supplying a font to the `setFont()` function, and each aspect of its appearance can be adjusted to give the desired effect. `setFontFamily()` and `setFontPointSize()` define the font's family (e.g. `Times`) and printed size; `setFontWeight()` and `setFontItalic()` provide control over the style of the font. `setFontUnderline()`, `setFontOverline()`, `setFontStrikeOut()`, and `setFontFixedPitch()` provide additional effects for text.
&emsp;&emsp;The color is set with `setForeground()`. If the text is intended to be used as an anchor (for hyperlinks), this can be enabled with `setAnchor()`. The `setAnchorHref()` and `setAnchorNames()` functions are used to specify the information about the hyperlink's destination and the anchor's name.

### Member Type Documentation

- enum `QTextCharFormat::UnderlineStyle`: This enum describes the different ways drawing underlined text.

Constant                               | Value | Description
---------------------------------------|-------|------------
`QTextCharFormat::NoUnderline`         | `0`   | Text is draw without any underlining decoration.
`QTextCharFormat::SingleUnderline`     | `1`   | A line is drawn using `Qt::SolidLine`.
`QTextCharFormat::DashUnderline`       | `2`   | Dashes are drawn using `Qt::DashLine`.
`QTextCharFormat::DotLine`             | `3`   | Dots are drawn using `Qt::DotLine`.
`QTextCharFormat::DashDotLine`         | `4`   | Dashs and dots are drawn using `Qt::DashDotLine`.
`QTextCharFormat::DashDotDotLine`      | `5`   | Underlines draw drawn using `Qt::DashDotDotLine`.
`QTextCharFormat::WaveUnderline`       | `6`   | The text is underlined using a wave shaped line.
`QTextCharFormat::SpellCheckUnderline` | `7`   | The underline is drawn depending on the `QStyle::SH_SpellCeckUnderlineStyle` style hint of the `QApplication` style. By default this is mapped to `WaveUnderline`, on `Mac OS X` it is mapped to `DashDotLine`.

- enum `QTextCharFormat::VerticalAlignment`: This enum describes the ways that adjacent characters can be vertically aligned.

Constant                            | Value | Description
------------------------------------|-------|------------
`QTextCharFormat::AlignNormal`      | `0`   | Adjacent characters are positioned in the standard way for text in the writing system in use.
`QTextCharFormat::AlignSuperScript` | `1`   | Characters are placed above the base line for normal text.
`QTextCharFormat::AlignSubScript`   | `2`   | Characters are placed below the base line for normal text.
`QTextCharFormat::AlignMiddle`      | `3`   | The center of the object is vertically aligned with the base line. Currently, this is only implemented for inline objects.
`QTextCharFormat::AlignBottom`      | `5`   | The bottom edge of the object is vertically aligned with the base line.
`QTextCharFormat::AlignTop`         | `4`   | The top edge of the object is vertically aligned with the base line.
`QTextCharFormat::AlignBaseline`    | `6`   | The base lines of the characters are aligned.

### Member Function Documentation

- `QTextCharFormat::QTextCharFormat()`: Constructs a new character format object.
- `QString QTextCharFormat::anchorHref() const`: Returns the text format's hypertext link, or an empty string if none has been set.
- `QStringList QTextCharFormat::anchorNames() const`: Returns the anchor names associated with this text format, or an empty string list if none has been set. If the anchor names are set, text with this format can be the destination of a hypertext link.
- `QFont QTextCharFormat::font() const`: Returns the font for this character format.
- `QFont::Capitalization QTextCharFormat::fontCapitalization() const`: Returns the current capitalization type of the font.
- `QString QTextCharFormat::fontFamily() const`: Returns the text format's font family.
- `bool QTextCharFormat::fontFixedPitch() const`: Returns `true` if the text format's font is fixed pitch; otherwise returns `false`.
- `QFont::HintingPreference QTextCharFormat::fontHintingPreference() const`: Returns the hinting preference set for this text format.
- `bool QTextCharFormat::fontItalic() const`: Returns `true` if the text format's font is italic; otherwise returns `false`.
- `bool QTextCharFormat::fontKerning() const`: Returns `true` if the font kerning is enabled.
- `qreal QTextCharFormat::fontLetterSpacing() const`: Returns the current letter spacing percentage.
- `bool QTextCharFormat::fontOverline() const`: Returns `true` if the text format's font is overlined; otherwise returns `false`.
- `qreal QTextCharFormat::fontPointSize() const`: Returns the font size used to display text in this format.
- `bool QTextCharFormat::fontStrikeOut() const`: Returns `true` if the text format's font is struck out (has a horizontal line drawn through it); otherwise returns `false`.
- `QFont::StyleHint QTextCharFormat::fontStyleHint() const`: Returns the font style hint.
- `QFont::StyleStrategy QTextCharFormat::fontStyleStrategy() const`: Returns the current font style strategy.
- `bool QTextCharFormat::fontUnderline() const`: Returns `true` if the text format's font is underlined; otherwise returns `false`.
- `int QTextCharFormat::fontWeight() const`: Returns the text format's font weight.
- `qreal QTextCharFormat::fontWordSpacing() const`: Returns the current word spacing value.
- `bool QTextCharFormat::isAnchor() const`: Returns `true` if the text is formatted as an anchor; otherwise returns `false`.
- `bool QTextCharFormat::isValid() const`: Returns `true` if this character format is valid; otherwise returns `false`.
- `void QTextCharFormat::setAnchor(bool anchor)`: If `anchor` is `true`, text with this format represents an anchor, and is formatted in the appropriate way; otherwise the text is formatted normally (Anchors are hyperlinks which are often shown underlined and in a different color from plain text). The way the text is rendered is independent of whether or not the format has a valid `anchor` defined. Use `setAnchorHref()`, and optionally `setAnchorNames()` to create a hypertext link.
- `void QTextCharFormat::setAnchorHref(const QString & value)`: Sets the hypertext link for the text format to the given `value`. This is typically a `URL` like `http://example.com/index.html`. The anchor will be displayed with the value as its display text; if you want to display different text call `setAnchorNames()`. To format the text as a hypertext link use `setAnchor()`.
- `void QTextCharFormat::setAnchorNames(const QStringList & names)`: Sets the text format's anchor `names`. For the anchor to work as a hyperlink, the destination must be set with `setAnchorHref()` and the anchor must be enabled with `setAnchor()`.
- `void QTextCharFormat::setFont(const QFont & font)`: Sets the text format's `font`.
- `void QTextCharFormat::setFontCapitalization(QFont::Capitalization capitalization)`: Sets the capitalization of the text that apppears in this font to `capitalization`. A font's capitalization makes the text appear in the selected capitalization mode.
- `void QTextCharFormat::setFontFamily(const QString & family)`: Sets the text format's font `family`.
- `void QTextCharFormat::setFontFixedPitch(bool fixedPitch)`: If `fixedPitch` is `true`, sets the text format's font to be fixed pitch; otherwise a `non-fixed` pitch font is used.
- `void QTextCharFormat::setFontHintingPreference(QFont::HintingPreference hintingPreference)`: Sets the hinting preference of the text format's font to be `hintingPreference`.
- `void QTextCharFormat::setFontItalic(bool italic)`: If `italic` is `true`, sets the text format's font to be italic; otherwise the font will be `non-italic`.
- `void QTextCharFormat::setFontKerning(bool enable)`: Enables kerning for this font if `enable` is `true`; otherwise disables it. When kerning is enabled, glyph metrics do not add up anymore, even for `Latin` text. In other words, the assumption that `width('a') + width('b')` is equal to `width("ab")` is not neccesairly `true`.
- `void QTextCharFormat::setFontLetterSpacing(qreal spacing)`: Sets the letter spacing of this format to the given `spacing`, in percent. A value of `100` indicates default `spacing`; a value of `200` doubles the amount of space a letter takes.
- `void QTextCharFormat::setFontOverline(bool overline)`: If `overline` is `true`, sets the text format's font to be overlined; otherwise the font is displayed `non-overlined`.
- `void QTextCharFormat::setFontPointSize(qreal size)`: Sets the text format's font `size`.
- `void QTextCharFormat::setFontStrikeOut(bool strikeOut)`: If `strikeOut` is `true`, sets the text format's font with `strike-out` enabled (with a horizontal line through it); otherwise it is displayed without strikeout.
- `void QTextCharFormat::setFontStyleHint(QFont::StyleHint hint, QFont::StyleStrategy strategy = QFont::PreferDefault)`: Sets the font style `hint` and `strategy`. `Qt` does not support style hints on `X11` since this information is not provided by the window system.
- `void QTextCharFormat::setFontStyleStrategy(QFont::StyleStrategy strategy)`: Sets the font style `strategy`.
- `void QTextCharFormat::setFontUnderline(bool underline)`: If `underline` is `true`, sets the text format's font to be underlined; otherwise it is displayed `non-underlined`.
- `void QTextCharFormat::setFontWeight(int weight)`: Sets the text format's font weight to `weight`.
- `void QTextCharFormat::setFontWordSpacing(qreal spacing)`: Sets the word spacing of this format to the given `spacing`, in pixels.
- `void QTextCharFormat::setTextOutline(const QPen & pen)`: Sets the pen used to draw the outlines of characters to the given `pen`.
- `void QTextCharFormat::setToolTip(const QString & text)`: Sets the tool tip for a fragment of text to the given `text`.
- `void QTextCharFormat::setUnderlineColor(const QColor & color)`: Sets the underline color used for the characters with this format to the `color` specified.
- `void QTextCharFormat::setUnderlineStyle(UnderlineStyle style)`: Sets the style of underlining the text to `style`.
- `void QTextCharFormat::setVerticalAlignment(VerticalAlignment alignment)`: Sets the vertical alignment used for the characters with this format to the `alignment` specified.
- `QPen QTextCharFormat::textOutline() const`: Returns the pen used to draw the outlines of characters in this format.
- `QString QTextCharFormat::toolTip() const`: Returns the tool tip that is displayed for a fragment of text.
- `QColor QTextCharFormat::underlineColor() const`: Returns the color used to underline the characters with this format.
- `UnderlineStyle QTextCharFormat::underlineStyle() const`: Returns the style of underlining the text.
- `VerticalAlignment QTextCharFormat::verticalAlignment() const`: Returns the vertical alignment used for characters with this format.