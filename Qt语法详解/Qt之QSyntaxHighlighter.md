---
title: Qt之QSyntaxHighlighter
date: 2019-01-26 20:46:10
categories: Qt语法详解
---
&emsp;&emsp;The `QSyntaxHighlighter` class allows you to define syntax highlighting rules, and in addition you can use the class to query a document's current formatting or user data.

Header               | Since    | Inherits
---------------------|----------|---------
`QSyntaxHighlighter` | `Qt 4.1` | `QObject`

**Note**: All functions in this class are reentrant.

### Public Functions

Return            | Function
------------------|---------
                  | `QSyntaxHighlighter(QObject * parent)`
                  | `QSyntaxHighlighter(QTextDocument * parent)`
                  | `QSyntaxHighlighter(QTextEdit * parent)`
`virtual`         | `~QSyntaxHighlighter()`
`QTextDocument *` | `document() const`
`void`            | `setDocument(QTextDocument * doc)`

### Public Slots

- `void rehighlight()`
- `void rehighlightBlock(const QTextBlock & block)`

### Protected Functions

Return                 | Function
-----------------------|----------
`QTextBlock`           | `currentBlock() const`
`int`                  | `currentBlockState() const`
`QTextBlockUserData *` | `currentBlockUserData() const`
`QTextCharFormat`      | `format(int position) const`
`virtual void`         | `highlightBlock(const QString & text) = 0`
`int`                  | `previousBlockState() const`
`void`                 | `setCurrentBlockState(int newState)`
`void`                 | `setCurrentBlockUserData(QTextBlockUserData * data)`
`void`                 | `setFormat(int start, int count, const QTextCharFormat & format)`
`void`                 | `setFormat(int start, int count, const QColor & color)`
`void`                 | `setFormat(int start, int count, const QFont & font)`

### Detailed Description

&emsp;&emsp;The `QSyntaxHighlighter` class allows you to define syntax highlighting rules, and in addition you can use the class to query a document's current formatting or user data.
&emsp;&emsp;The `QSyntaxHighlighter` class is a base class for implementing `QTextEdit` syntax highlighters. A syntax highligher automatically highlights parts of the text in a `QTextEdit`, or more generally in a `QTextDocument`. Syntax highlighters are often used when the user is entering text in a specific format (for example source code) and help the user to read the text and identify syntax errors.
&emsp;&emsp;To provide your own syntax highlighting, you must subclass `QSyntaxHighlighter` and reimplement `highlightBlock()`.
&emsp;&emsp;When you create an instance of your `QSyntaxHighlighter` subclass, pass it the `QTextEdit` or `QTextDocument` that you want the syntax highlighting to be applied to. For example:

``` cpp
QTextEdit *editor = new QTextEdit;
MyHighlighter *highlighter = new MyHighlighter ( editor->document() );
```

After this your `highlightBlock()` function will be called automatically whenever necessary. Use your `highlightBlock()` function to apply formatting (e.g. setting the font and color) to the text that is passed to it. `QSyntaxHighlighter` provides the `setFormat()` function which applies a given `QTextCharFormat` on the current text block. For example:

``` cpp
void MyHighlighter::highlightBlock ( const QString &text ) {
    QTextCharFormat myClassFormat;
    myClassFormat.setFontWeight ( QFont::Bold );
    myClassFormat.setForeground ( Qt::darkMagenta );
    QString pattern = "\\bMy[A-Za-z]+\\b";
    QRegExp expression ( pattern );
    int index = text.indexOf ( expression );
​
    while ( index >= 0 ) {
        int length = expression.matchedLength();
        setFormat ( index, length, myClassFormat );
        index = text.indexOf ( expression, index + length );
    }
}
```

&emsp;&emsp;Some syntaxes can have constructs that span several text blocks. For example, a `C++` syntax highlighter should be able to cope with `/*...*/` multiline comments. To deal with these cases it is necessary to know the end state of the previous text block (e.g. `in comment`).
&emsp;&emsp;Inside your `highlightBlock()` implementation you can query the end state of the previous text block using the `previousBlockState()` function. After parsing the block you can save the last state using `setCurrentBlockState()`.
&emsp;&emsp;The `currentBlockState()` and `previousBlockState()` functions return an int value. If no state is set, the returned value is `-1`. You can designate any other value to identify any given state using the `setCurrentBlockState()` function. Once the state is set the `QTextBlock` keeps that value until it is set set again or until the corresponding paragraph of text is deleted.
&emsp;&emsp;For example, if you're writing a simple `C++` syntax highlighter, you might designate `1` to signify `in comment`:

``` cpp
QTextCharFormat multiLineCommentFormat;
multiLineCommentFormat.setForeground ( Qt::red );
​
QRegExp startExpression ( "/\\*" );
QRegExp endExpression ( "\\*/" );
​
setCurrentBlockState ( 0 );
​
int startIndex = 0;
​
if ( previousBlockState() != 1 ) {
    startIndex = text.indexOf ( startExpression );
}
​
while ( startIndex >= 0 ) {
    int endIndex = text.indexOf ( endExpression, startIndex );
    int commentLength;
​
    if ( endIndex == -1 ) {
        setCurrentBlockState ( 1 );
        commentLength = text.length() - startIndex;
    } else {
        commentLength = endIndex - startIndex + endExpression.matchedLength();
    }
​
    setFormat ( startIndex, commentLength, multiLineCommentFormat );
    startIndex = text.indexOf ( startExpression, startIndex + commentLength );
}
```

&emsp;&emsp;In the example above, we first set the current block state to `0`. Then, if the previous block ended within a comment, we higlight from the beginning of the current block (`startIndex = 0`). Otherwise, we search for the given start expression. If the specified end expression cannot be found in the text block, we change the current block state by calling `setCurrentBlockState()`, and make sure that the rest of the block is higlighted.
&emsp;&emsp;In addition you can query the current formatting and user data using the `format()` and `currentBlockUserData()` functions respectively. You can also attach user data to the current text block using the `setCurrentBlockUserData()` function. `QTextBlockUserData` can be used to store custom settings. In the case of syntax highlighting, it is in particular interesting as cache storage for information that you may figure out while parsing the paragraph's text.

### Member Function Documentation

- `QSyntaxHighlighter::QSyntaxHighlighter(QObject * parent)`: Constructs a `QSyntaxHighlighter` with the given `parent`.
- `QSyntaxHighlighter::QSyntaxHighlighter(QTextDocument * parent)`: Constructs a `QSyntaxHighlighter` and installs it on `parent`. The specified `QTextDocument` also becomes the owner of the `QSyntaxHighlighter`.
- `QSyntaxHighlighter::QSyntaxHighlighter(QTextEdit * parent)`: Constructs a `QSyntaxHighlighter` and installs it on `parent's` `QTextDocument`. The specified `QTextEdit` also becomes the owner of the `QSyntaxHighlighter`.
- `QSyntaxHighlighter::~QSyntaxHighlighter() [virtual]`: Destructor. Uninstalls this syntax highlighter from the text document.
- `QTextBlock QSyntaxHighlighter::currentBlock() const [protected]`: Returns the current text block.
- `int QSyntaxHighlighter::currentBlockState() const [protected]`: Returns the state of the current text block. If no value is set, the returned value is `-1`.
- `QTextBlockUserData * QSyntaxHighlighter::currentBlockUserData() const [protected]`: Returns the `QTextBlockUserData` object previously attached to the current text block.
- `QTextDocument * QSyntaxHighlighter::document() const`: Returns the `QTextDocument` on which this syntax highlighter is installed.
- `QTextCharFormat QSyntaxHighlighter::format(int position) const [protected]`: Returns the format at `position` inside the syntax highlighter's current text block.
- `void QSyntaxHighlighter::highlightBlock(const QString & text) [pure virtual protected]`: Highlights the given `text` block. This function is called when necessary by the rich text engine, i.e. on `text` blocks which have changed. To provide your own syntax highlighting, you must subclass `QSyntaxHighlighter` and reimplement `highlightBlock()`. In your reimplementation you should parse the block's `text` and call `setFormat()` as often as necessary to apply any font and color changes that you require. For example:

``` cpp
void MyHighlighter::highlightBlock ( const QString &text ) {
    QTextCharFormat myClassFormat;
    myClassFormat.setFontWeight ( QFont::Bold );
    myClassFormat.setForeground ( Qt::darkMagenta );
    QString pattern = "\\bMy[A-Za-z]+\\b";
    QRegExp expression ( pattern );
    int index = text.indexOf ( expression );
​
    while ( index >= 0 ) {
        int length = expression.matchedLength();
        setFormat ( index, length, myClassFormat );
        index = text.indexOf ( expression, index + length );
    }
}
```

&emsp;&emsp;Some syntaxes can have constructs that span several text blocks. For example, a `C++` syntax highlighter should be able to cope with `/*...*/` multiline comments. To deal with these cases it is necessary to know the end state of the previous text block (e.g. `in comment`).
&emsp;&emsp;Inside your `highlightBlock()` implementation you can query the end state of the previous text block using the `previousBlockState()` function. After parsing the block you can save the last state using `setCurrentBlockState()`.
&emsp;&emsp;The `currentBlockState()` and `previousBlockState()` functions return an int value. If no state is set, the returned value is `-1`. You can designate any other value to identify any given state using the `setCurrentBlockState()` function. Once the state is set the `QTextBlock` keeps that value until it is set set again or until the corresponding paragraph of text gets deleted.
&emsp;&emsp;For example, if you're writing a simple `C++` syntax highlighter, you might designate `1` to signify `in comment`. For a text block that ended in the middle of a comment you'd set `1` using `setCurrentBlockState`, and for other paragraphs you'd set `0`. In your parsing code if the return value of `previousBlockState()` is `1`, you would highlight the text as a `C++` comment until you reached the closing `*/`.

- `int QSyntaxHighlighter::previousBlockState() const [protected]`: Returns the end state of the text block previous to the syntax highlighter's current block. If no value was previously set, the returned value is `-1`.
- `void QSyntaxHighlighter::rehighlight() [slot]`: Reapplies the highlighting to the whole document.
- `void QSyntaxHighlighter::rehighlightBlock(const QTextBlock & block) [slot]`: Reapplies the highlighting to the given `QTextBlock` `block`.
- `void QSyntaxHighlighter::setCurrentBlockState(int newState) [protected]`: Sets the state of the current text block to `newState`.
- `void QSyntaxHighlighter::setCurrentBlockUserData(QTextBlockUserData * data) [protected]`: Attaches the given `data` to the current text block. The ownership is passed to the underlying text document, i.e. the provided `QTextBlockUserData` object will be deleted if the corresponding text block gets deleted. `QTextBlockUserData` can be used to store custom settings. In the case of syntax highlighting, it is in particular interesting as cache storage for information that you may figure out while parsing the paragraph's text. For example while parsing the text, you can keep track of parenthesis characters that you encounter (`{[(` and the like), and store their relative position and the actual `QChar` in a simple class derived from `QTextBlockUserData`:

``` cpp
struct ParenthesisInfo {
    QChar char;
    int position;
};
​
struct BlockData : public QTextBlockUserData {
    QVector<ParenthesisInfo> parentheses;
};
```

&emsp;&emsp;During cursor navigation in the associated editor, you can ask the current `QTextBlock` (retrieved using the `QTextCursor::block()` function) if it has a user data object set and cast it to your `BlockData` object. Then you can check if the current cursor position matches with a previously recorded parenthesis position, and, depending on the type of parenthesis (opening or closing), find the next opening or closing parenthesis on the same level.
&emsp;&emsp;In this way you can do a visual parenthesis matching and highlight from the current cursor position to the matching parenthesis. That makes it easier to spot a missing parenthesis in your code and to find where a corresponding opening/closing parenthesis is when editing parenthesis intensive code.

- `void QSyntaxHighlighter::setDocument(QTextDocument * doc)`: Installs the syntax highlighter on the given `QTextDocument` `doc`. A `QSyntaxHighlighter` can only be used with one document at a time.
- `void QSyntaxHighlighter::setFormat(int start, int count, const QTextCharFormat & format) [protected]`: This function is applied to the syntax highlighter's current text block (i.e. the text that is passed to the `highlightBlock()` function). The specified `format` is applied to the text from the `start` position for a length of `count` characters (if `count` is `0`, nothing is done). The formatting properties set in `format` are merged at display time with the formatting information stored directly in the document, for example as previously set with `QTextCursor's` functions. Note that the document itself remains unmodified by the `format` set through this function.
- `void QSyntaxHighlighter::setFormat(int start, int count, const QColor & color) [protected]`: This is an overloaded function. The specified `color` is applied to the current text block from the `start` position for a length of `count` characters. The other attributes of the current text block, e.g. the font and background color, are reset to default values.
- `void QSyntaxHighlighter::setFormat(int start, int count, const QFont & font) [protected]`: This is an overloaded function. The specified `font` is applied to the current text block from the `start` position for a length of `count` characters. The other attributes of the current text block, e.g. the font and background color, are reset to default values.