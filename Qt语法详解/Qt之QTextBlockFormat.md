---
title: Qt之QTextBlockFormat
categories: Qt语法详解
abbrlink: 6bf6c7b
date: 2019-01-23 10:19:56
---
&emsp;&emsp;The `QTextBlockFormat` class provides formatting information for blocks of text in a `QTextDocument`.<!--more-->

Header             | Inherits
-------------------|---------
`QTextBlockFormat` | `QTextFormat`

**Note**: All functions in this class are reentrant.

### Public Functions

Return                    | Function
--------------------------|---------
                          | `QTextBlockFormat()`
`Qt::Alignment`           | `alignment() const`
`qreal`                   | `bottomMargin() const`
`int`                     | `indent() const`
`bool`                    | `isValid() const`
`qreal`                   | `leftMargin() const`
`qreal`                   | `lineHeight(qreal scriptLineHeight, qreal scaling) const`
`qreal`                   | `lineHeight() const`
`int`                     | `lineHeightType() const`
`bool`                    | `nonBreakableLines() const`
`PageBreakFlags`          | `pageBreakPolicy() const`
`qreal`                   | `rightMargin() const`
`void`                    | `setAlignment(Qt::Alignment alignment)`
`void`                    | `setBottomMargin(qreal margin)`
`void`                    | `setIndent(int indentation)`
`void`                    | `setLeftMargin(qreal margin)`
`void`                    | `setLineHeight(qreal height, int heightType)`
`void`                    | `setNonBreakableLines(bool b)`
`void`                    | `setPageBreakPolicy(PageBreakFlags policy)`
`void`                    | `setRightMargin(qreal margin)`
`void`                    | `setTabPositions(const QList<QTextOption::Tab> & tabs)`
`void`                    | `setTextIndent(qreal indent)`
`void`                    | `setTopMargin(qreal margin)`
`QList<QTextOption::Tab>` | `tabPositions() const`
`qreal`                   | `textIndent() const`
`qreal`                   | `topMargin() const`

### Detailed Description

&emsp;&emsp;The `QTextBlockFormat` class provides formatting information for blocks of text in a `QTextDocument`.
&emsp;&emsp;A document is composed of a list of blocks, represented by `QTextBlock` objects. Each block can contain an item of some kind, such as a paragraph of text, a table, a list, or an image. Every block has an associated `QTextBlockFormat` that specifies its characteristics.
&emsp;&emsp;To cater for `left-to-right` and `right-to-left` languages you can set a block's direction with `setDirection()`. Paragraph alignment is set with `setAlignment()`. Margins are controlled by `setTopMargin()`, `setBottomMargin()`, `setLeftMargin()`, `setRightMargin()`. Overall indentation is set with `setIndent()`, the indentation of the first line with `setTextIndent()`.
&emsp;&emsp;Line spacing is set with `setLineHeight()` and retrieved via `lineHeight()` and `lineHeightType()`. The types of line spacing available are in the `LineHeightTypes` enum.
&emsp;&emsp;Line breaking can be enabled and disabled with `setNonBreakableLines()`.
&emsp;&emsp;The brush used to paint the paragraph's background is set with `setBackground()`, and other aspects of the text's appearance can be customized by using the `setProperty()` function with the `OutlinePen`, `ForegroundBrush`, and `BackgroundBrush` `QTextFormat::Property` values.
&emsp;&emsp;If a text block is part of a list, it can also have a list format that is accessible with the `listFormat()` function.

### Member Type Documentation

&emsp;&emsp;enum `QTextBlockFormat::LineHeightTypes`: This enum describes the various types of line spacing support paragraphs can have.

Constant                               | Value | Description
---------------------------------------|-------|------------
`QTextBlockFormat::SingleHeight`       | `0`   | This is the default line height: single spacing.
`QTextBlockFormat::ProportionalHeight` | `1`   | This sets the spacing proportional to the line (in percentage). For example, set to `200` for double spacing.
`QTextBlockFormat::FixedHeight`        | `2`   | This sets the line height to a fixed line height (in pixels).
`QTextBlockFormat::MinimumHeight`      | `3`   | This sets the minimum line height (in pixels).
`QTextBlockFormat::LineDistanceHeight` | `4`   | This adds the specified height between lines (in pixels).

### Member Function Documentation

- `QTextBlockFormat::QTextBlockFormat()`: Constructs a new `QTextBlockFormat`.
- `Qt::Alignment QTextBlockFormat::alignment() const`: Returns the paragraph's alignment.
- `qreal QTextBlockFormat::bottomMargin() const`: Returns the paragraph's bottom margin.
- `int QTextBlockFormat::indent() const`: Returns the paragraph's indent.
- `bool QTextBlockFormat::isValid() const`: Returns `true` if this block format is valid; otherwise returns `false`.
- `qreal QTextBlockFormat::leftMargin() const`: Returns the paragraph's left margin.
- `qreal QTextBlockFormat::lineHeight(qreal scriptLineHeight, qreal scaling) const`: Returns the height of the lines in the paragraph based on the height of the script line given by `scriptLineHeight` and the specified `scaling` factor. The value that is returned is also dependent on the given `LineHeightType` of the paragraph as well as the `LineHeight` setting that has been set for the paragraph. The `scaling` is needed for heights that include a fixed number of pixels, to scale them appropriately for printing.
- `qreal QTextBlockFormat::lineHeight() const`: This returns the `LineHeight` property for the paragraph.
- `int QTextBlockFormat::lineHeightType() const`: This returns the `LineHeightType` property of the paragraph.
- `bool QTextBlockFormat::nonBreakableLines() const`: Returns `true` if the lines in the paragraph are `non-breakable`; otherwise returns `false`.
- `PageBreakFlags QTextBlockFormat::pageBreakPolicy() const`: Returns the currently set page break policy for the paragraph. The default is `QTextFormat::PageBreak_Auto`.
- `qreal QTextBlockFormat::rightMargin() const`: Returns the paragraph's right margin.
- `void QTextBlockFormat::setAlignment(Qt::Alignment alignment)`: Sets the paragraph's `alignment`.
- `void QTextBlockFormat::setBottomMargin(qreal margin)`: Sets the paragraph's bottom `margin`.
- `void QTextBlockFormat::setIndent(int indentation)`: Sets the paragraph's `indentation`. Margins are set independently of `indentation` with `setLeftMargin()` and `setTextIndent()`. The `indentation` is an integer that is multiplied with the `document-wide` standard indent, resulting in the actual indent of the paragraph.
- `void QTextBlockFormat::setLeftMargin(qreal margin)`: Sets the paragraph's left `margin`. Indentation can be applied separately with `setIndent()`.
- `void QTextBlockFormat::setLineHeight(qreal height, int heightType)`: Sets the line height for the paragraph to the value given by `height` which is dependent on `heightType` in the way described by the `LineHeightTypes` enum.
- `void QTextBlockFormat::setNonBreakableLines(bool b)`: If `b` is `true`, the lines in the paragraph are treated as `non-breakable`; otherwise they are breakable.
- `void QTextBlockFormat::setPageBreakPolicy(PageBreakFlags policy)` -- Sets the page break policy for the paragraph to `policy`.
- `void QTextBlockFormat::setRightMargin(qreal margin)`: Sets the paragraph's right `margin`.
- `void QTextBlockFormat::setTabPositions(const QList<QTextOption::Tab> & tabs)`: Sets the tab positions for the text block to those specified by `tabs`.
- `void QTextBlockFormat::setTextIndent(qreal indent)`: Sets the `indent` for the first line in the block. This allows the first line of a paragraph to be indented differently to the other lines, enhancing the readability of the text.
- `void QTextBlockFormat::setTopMargin(qreal margin)`: Sets the paragraph's top `margin`.
- `QList<QTextOption::Tab> QTextBlockFormat::tabPositions() const`: Returns a list of tab positions defined for the text block.
- `qreal QTextBlockFormat::textIndent() const`: Returns the paragraph's text indent.
- `qreal QTextBlockFormat::topMargin() const`: Returns the paragraph's top margin.