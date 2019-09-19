---
title: Qt之QTextCursor
date: 2019-01-29 11:13:18
categories: Qt语法详解
---
&emsp;&emsp;The `QTextCursor` class offers an `API` to access and modify `QTextDocuments`. The header file is `QTextCursor`. **Note**: All functions in this class are reentrant.

### Public Functions

Return                  | Function
------------------------|---------
                        | `QTextCursor()`
                        | `QTextCursor(QTextDocument * document)`
                        | `QTextCursor(QTextFrame * frame)`
                        | `QTextCursor(const QTextBlock & block)`
                        | `QTextCursor(const QTextCursor & cursor)`
                        | `~QTextCursor()`
`int`                   | `anchor() const`
`bool`                  | `atBlockEnd() const`
`bool`                  | `atBlockStart() const`
`bool`                  | `atEnd() const`
`bool`                  | `atStart() const`
`void`                  | `beginEditBlock()`
`QTextBlock`            | `block() const`
`QTextCharFormat`       | `blockCharFormat() const`
`QTextBlockFormat`      | `blockFormat() const`
`int`                   | `blockNumber() const`
`QTextCharFormat`       | `charFormat() const`
`void`                  | `clearSelection()`
`int`                   | `columnNumber() const`
`QTextList *`           | `createList(const QTextListFormat & format)`
`QTextList *`           | `createList(QTextListFormat::Style style)`
`QTextFrame *`          | `currentFrame() const`
`QTextList *`           | `currentList() const`
`QTextTable *`          | `currentTable() const`
`void`                  | `deleteChar()`
`void`                  | `deletePreviousChar()`
`QTextDocument *`       | `document() const`
`void`                  | `endEditBlock()`
`bool`                  | `hasComplexSelection() const`
`bool`                  | `hasSelection() const`
`void`                  | `insertBlock()`
`void`                  | `insertBlock(const QTextBlockFormat & format)`
`void`                  | `insertBlock(const QTextBlockFormat & format, const QTextCharFormat & charFormat)`
`void`                  | `insertFragment(const QTextDocumentFragment & fragment)`
`QTextFrame *`          | `insertFrame(const QTextFrameFormat & format)`
`void`                  | `insertHtml(const QString & html)`
`void`                  | `insertImage(const QTextImageFormat & format)`
`void`                  | `insertImage(const QTextImageFormat & format, QTextFrameFormat::Position alignment)`
`void`                  | `insertImage(const QString & name)`
`void`                  | `insertImage(const QImage & image, const QString & name = QString())`
`QTextList *`           | `insertList(const QTextListFormat & format)`
`QTextList *`           | `insertList(QTextListFormat::Style style)`
`QTextTable *`          | `insertTable(int rows, int columns, const QTextTableFormat & format)`
`QTextTable *`          | `insertTable(int rows, int columns)`
`void`                  | `insertText(const QString & text)`
`void`                  | `insertText(const QString & text, const QTextCharFormat & format)`
`bool`                  | `isCopyOf(const QTextCursor & other) const`
`bool`                  | `isNull() const`
`void`                  | `joinPreviousEditBlock()`
`bool`                  | `keepPositionOnInsert() const`
`void`                  | `mergeBlockCharFormat(const QTextCharFormat & modifier)`
`void`                  | `mergeBlockFormat(const QTextBlockFormat & modifier)`
`void`                  | `mergeCharFormat(const QTextCharFormat & modifier)`
`bool`                  | `movePosition(MoveOperation operation, MoveMode mode = MoveAnchor, int n = 1)`
`int`                   | `position() const`
`int`                   | `positionInBlock() const`
`void`                  | `removeSelectedText()`
`void`                  | `select(SelectionType selection)`
`void`                  | `selectedTableCells(int * firstRow, int * numRows, int * firstColumn, int * numColumns) const`
`QString`               | `selectedText() const`
`QTextDocumentFragment` | `selection() const`
`int`                   | `selectionEnd() const`
`int`                   | `selectionStart() const`
`void`                  | `setBlockCharFormat(const QTextCharFormat & format)`
`void`                  | `setBlockFormat(const QTextBlockFormat & format)`
`void`                  | `setCharFormat(const QTextCharFormat & format)`
`void`                  | `setKeepPositionOnInsert(bool b)`
`void`                  | `setPosition(int pos, MoveMode m = MoveAnchor)`
`void`                  | `setVerticalMovementX(int x)`
`void`                  | `setVisualNavigation(bool b)`
`int`                   | `verticalMovementX() const`
`bool`                  | `visualNavigation() const`
`bool`                  | `operator!=(const QTextCursor & other) const`
`bool`                  | `operator<(const QTextCursor & other) const`
`bool`                  | `operator<=(const QTextCursor & other) const`
`QTextCursor &`         | `operator=(const QTextCursor & cursor)`
`bool`                  | `operator==(const QTextCursor & other) const`
`bool`                  | `operator>(const QTextCursor & other) const`
`bool`                  | `operator>=(const QTextCursor & other) const`

### Detailed Description

&emsp;&emsp;The `QTextCursor` class offers an `API` to access and modify `QTextDocuments`.
&emsp;&emsp;Text cursors are objects that are used to access and modify the contents and underlying structure of text documents via a programming interface that mimics the behavior of a cursor in a text editor. `QTextCursor` contains information about both the cursor's position within a `QTextDocument` and any selection that it has made.
&emsp;&emsp;`QTextCursor` is modeled on the way a text cursor behaves in a text editor, providing a programmatic means of performing standard actions through the user interface. A document can be thought of as a single string of characters. The cursor's current `position()` then is always either between two consecutive characters in the string, or else before the very first character or after the very last character in the string. Documents can also contain tables, lists, images, and other objects in addition to text but, from the developer's point of view, the document can be treated as one long string. Some portions of that string can be considered to lie within particular blocks (e.g. paragraphs), or within a table's cell, or a list's item, or other structural elements. When we refer to `current character` we mean the character immediately before the cursor `position()` in the document. Similarly, the `current block` is the block that contains the cursor `position()`.
&emsp;&emsp;A `QTextCursor` also has an `anchor()` position. The text that is between the `anchor()` and the `position()` is the selection. If `anchor() == position()` there is no selection.
&emsp;&emsp;The cursor position can be changed programmatically using `setPosition()` and `movePosition()`; the latter can also be used to select text. For selections see `selectionStart()`, `selectionEnd()`, `hasSelection()`, `clearSelection()`, and `removeSelectedText()`.
&emsp;&emsp;If the `position()` is at the start of a block `atBlockStart()` returns `true`; and if it is at the end of a block `atBlockEnd()` returns `true`. The format of the current character is returned by `charFormat()`, and the format of the current block is returned by `blockFormat()`.
&emsp;&emsp;Formatting can be applied to the current text document using the `setCharFormat()`, `mergeCharFormat()`, `setBlockFormat()` and `mergeBlockFormat()` functions. The `set` functions will replace the cursor's current character or block format, while the `merge` functions add the given format properties to the cursor's current format. If the cursor has a selection the given format is applied to the current selection. Note that when only parts of a block is selected the block format is applied to the entire block. The text at the current character position can be turned into a list using `createList()`.
&emsp;&emsp;Deletions can be achieved using `deleteChar()`, `deletePreviousChar()`, and `removeSelectedText()`.
&emsp;&emsp;Text strings can be inserted into the document with the `insertText()` function, blocks (representing new paragraphs) can be inserted with `insertBlock()`.
&emsp;&emsp;Existing fragments of text can be inserted with `insertFragment()`, but if you want to insert pieces of text in various formats, it is usually still easier to use `insertText()` and supply a character format.
&emsp;&emsp;Various types of `higher-level` structure can also be inserted into the document with the cursor:

- Lists are ordered sequences of block elements that are decorated with bullet points or symbols. These are inserted in a specified format with `insertList()`.
- Tables are inserted with the `insertTable()` function, and can be given an optional format. These contain an array of cells that can be traversed using the cursor.
- Inline images are inserted with `insertImage()`. The image to be used can be specified in an image format, or by name.
- Frames are inserted by calling `insertFrame()` with a specified format.

&emsp;&emsp;Actions can be grouped (i.e. treated as a single action for undo/redo) using `beginEditBlock()` and `endEditBlock()`.
&emsp;&emsp;Cursor movements are limited to valid cursor positions. In `Latin` writing this is between any two consecutive characters in the text, before the first character, or after the last character. In some other writing systems cursor movements are limited to `clusters` (e.g. a syllable in `Devanagari`, or a base letter plus diacritics). Functions such as `movePosition()` and `deleteChar()` limit cursor movement to these valid positions.

### Member Type Documentation

- enum `QTextCursor::MoveMode`:

Constant                  | Value | Description
--------------------------|-------|------------
`QTextCursor::MoveAnchor` | `0`   | Moves the anchor to the same position as the cursor itself.
`QTextCursor::KeepAnchor` | `1`   | Keeps the anchor where it is.

If the `anchor()` is kept where it is and the `position()` is moved, the text in between will be selected.

- enum `QTextCursor::MoveOperation`:

Constant                         | Value | Description
---------------------------------|-------|------------
`QTextCursor::NoMove`            | `0`   | Keep the cursor where it is.
`QTextCursor::Start`             | `1`   | Move to the start of the document.
`QTextCursor::StartOfLine`       | `3`   | Move to the start of the current line.
`QTextCursor::StartOfBlock`      | `4`   | Move to the start of the current block.
`QTextCursor::StartOfWord`       | `5`   | Move to the start of the current word.
`QTextCursor::PreviousBlock`     | `6`   | Move to the start of the previous block.
`QTextCursor::PreviousCharacter` | `7`   | Move to the previous character.
`QTextCursor::PreviousWord`      | `8`   | Move to the beginning of the previous word.
`QTextCursor::Up`                | `2`   | Move up one line.
`QTextCursor::Left`              | `9`   | Move left one character.
`QTextCursor::WordLeft`          | `10`  | Move left one word.
`QTextCursor::End`               | `11`  | Move to the end of the document.
`QTextCursor::EndOfLine`         | `13`  | Move to the end of the current line.
`QTextCursor::EndOfWord`         | `14`  | Move to the end of the current word.
`QTextCursor::EndOfBlock`        | `15`  | Move to the end of the current block.
`QTextCursor::NextBlock`         | `16`  | Move to the beginning of the next block.
`QTextCursor::NextCharacter`     | `17`  | Move to the next character.
`QTextCursor::NextWord`          | `18`  | Move to the next word.
`QTextCursor::Down`              | `12`  | Move down one line.
`QTextCursor::Right`             | `19`  | Move right one character.
`QTextCursor::WordRight`         | `20`  | Move right one word.
`QTextCursor::NextCell`          | `21`  | Move to the beginning of the next table cell inside the current table. If the current cell is the last cell in the row, the cursor will move to the first cell in the next row.
`QTextCursor::PreviousCell`      | `22`  | Move to the beginning of the previous table cell inside the current table. If the current cell is the first cell in the row, the cursor will move to the last cell in the previous row.
`QTextCursor::NextRow`           | `23`  | Move to the first new cell of the next row in the current table.
`QTextCursor::PreviousRow`       | `24`  | Move to the last cell of the previous row in the current table.

- enum `QTextCursor::SelectionType`: This enum describes the types of selection that can be applied with the `select()` function.

Constant                        | Value | Description
--------------------------------|-------|------------
`QTextCursor::Document`         | `3`   | Selects the entire document.
`QTextCursor::BlockUnderCursor` | `2`   | Selects the block of text under the cursor.
`QTextCursor::LineUnderCursor`  | `1`   | Selects the line of text under the cursor.
`QTextCursor::WordUnderCursor`  | `0`   | Selects the word under the cursor. If the cursor is not positioned within a string of selectable characters, no text is selected.

### Member Function Documentation

- `QTextCursor::QTextCursor()`: Constructs a null cursor.
- `QTextCursor::QTextCursor(QTextDocument * document)`: Constructs a cursor pointing to the beginning of the `document`.
- `QTextCursor::QTextCursor(QTextFrame * frame)`: Constructs a cursor pointing to the beginning of the `frame`.
- `QTextCursor::QTextCursor(const QTextBlock & block)`: Constructs a cursor pointing to the beginning of the `block`.
- `QTextCursor::QTextCursor(const QTextCursor & cursor)`: Constructs a new cursor that is a copy of `cursor`.
- `QTextCursor::~QTextCursor()`: Destroys the `QTextCursor`.
- `int QTextCursor::anchor() const`: Returns the anchor position; this is the same as `position()` unless there is a selection in which case `position()` marks one end of the selection and `anchor()` marks the other end. Just like the cursor position, the anchor position is between characters.
- `bool QTextCursor::atBlockEnd() const`: Returns `true` if the cursor is at the end of a block; otherwise returns `false`.
- `bool QTextCursor::atBlockStart() const`: Returns `true` if the cursor is at the start of a block; otherwise returns `false`.
- `bool QTextCursor::atEnd() const`: Returns `true` if the cursor is at the end of the document; otherwise returns `false`.
- `bool QTextCursor::atStart() const`: Returns `true` if the cursor is at the start of the document; otherwise returns `false`.
- `void QTextCursor::beginEditBlock()`: Indicates the start of a block of editing operations on the document that should appear as a single operation from an undo/redo point of view. For example:

``` cpp
QTextCursor cursor ( textDocument );
cursor.beginEditBlock();
cursor.insertText ( "Hello" );
cursor.insertText ( "World" );
cursor.endEditBlock();
​
textDocument->undo();
```

The call to `undo()` will cause both insertions to be undone, causing both `World` and `Hello` to be removed. It is possible to nest calls to `beginEditBlock` and `endEditBlock`. The `top-most` pair will determine the scope of the undo/redo operation.

- `QTextBlock QTextCursor::block() const`: Returns the block that contains the cursor.
- `QTextCharFormat QTextCursor::blockCharFormat() const`: Returns the block character format of the block the cursor is in. The block char format is the format used when inserting text at the beginning of an empty block.
- `QTextBlockFormat QTextCursor::blockFormat() const`: Returns the block format of the block the cursor is in.
- `int QTextCursor::blockNumber() const`: Returns the number of the block the cursor is in, or `0` if the cursor is invalid. Note that this function only makes sense in documents without complex objects such as tables or frames.
- `QTextCharFormat QTextCursor::charFormat() const`: Returns the format of the character immediately before the cursor `position()`. If the cursor is positioned at the beginning of a text block that is not empty then the format of the character immediately after the cursor is returned.
- `void QTextCursor::clearSelection()`: Clears the current selection by setting the anchor to the cursor position. Note that it does not delete the text of the selection.
- `int QTextCursor::columnNumber() const`: Returns the position of the cursor within its containing line. Note that this is the column number relative to a wrapped line, not relative to the block (i.e. the paragraph). You probably want to call `positionInBlock()` instead.
- `QTextList * QTextCursor::createList(const QTextListFormat & format)`: Creates and returns a new list with the given `format`, and makes the current paragraph the cursor is in the first list item.
- `QTextList * QTextCursor::createList(QTextListFormat::Style style)`: This is an overloaded function. Creates and returns a new list with the given `style`, making the cursor's current paragraph the first list item. The `style` to be used is defined by the `QTextListFormat::Style` enum.
- `QTextFrame * QTextCursor::currentFrame() const`: Returns a pointer to the current frame. Returns `0` if the cursor is invalid.
- `QTextList * QTextCursor::currentList() const`: Returns the current list if the cursor `position()` is inside a block that is part of a list; otherwise returns `0`.
- `QTextTable * QTextCursor::currentTable() const`: Returns a pointer to the current table if the cursor `position()` is inside a block that is part of a table; otherwise returns `0`.
- `void QTextCursor::deleteChar()`: If there is no selected text, deletes the character at the current cursor position; otherwise deletes the selected text.
- `void QTextCursor::deletePreviousChar()`: If there is no selected text, deletes the character before the current cursor position; otherwise deletes the selected text.
- `QTextDocument * QTextCursor::document() const`: Returns the document this cursor is associated with.
- `void QTextCursor::endEditBlock()`: Indicates the end of a block of editing operations on the document that should appear as a single operation from an undo/redo point of view.
- `bool QTextCursor::hasComplexSelection() const`: Returns `true` if the cursor contains a selection that is not simply a range from `selectionStart()` to `selectionEnd()`; otherwise returns `false`. Complex selections are ones that span at least two cells in a table; their extent is specified by `selectedTableCells()`.
- `bool QTextCursor::hasSelection() const`: Returns `true` if the cursor contains a selection; otherwise returns `false`.
- `void QTextCursor::insertBlock()`: Inserts a new empty block at the cursor `position()` with the current `blockFormat()` and `charFormat()`.
- `void QTextCursor::insertBlock(const QTextBlockFormat & format)`: This is an overloaded function. Inserts a new empty block at the cursor `position()` with block `format` and the current `charFormat()` as block char format.
- `void QTextCursor::insertBlock(const QTextBlockFormat & format, const QTextCharFormat & charFormat)`: This is an overloaded function. Inserts a new empty block at the cursor `position()` with block `format` and `charFormat` as block char format.
- `void QTextCursor::insertFragment(const QTextDocumentFragment & fragment)`: Inserts the text `fragment` at the current `position()`.
- `QTextFrame * QTextCursor::insertFrame(const QTextFrameFormat & format)`: Inserts a frame with the given `format` at the current cursor `position()`, moves the cursor `position()` inside the frame, and returns the frame. If the cursor holds a selection, the whole selection is moved inside the frame.
- `void QTextCursor::insertHtml(const QString & html)`: Inserts the text `html` at the current `position()`. The text is interpreted as `HTML`. **Note**: When using this function with a style sheet, the style sheet will only apply to the current block in the document. In order to apply a style sheet throughout a document, use `QTextDocument::setDefaultStyleSheet()` instead.
- `void QTextCursor::insertImage(const QTextImageFormat & format)`: Inserts the image defined by `format` at the current `position()`.
- `void QTextCursor::insertImage(const QTextImageFormat & format, QTextFrameFormat::Position alignment)`: This is an overloaded function. Inserts the image defined by the given `format` at the cursor's current position with the specified `alignment`.
- `void QTextCursor::insertImage(const QString & name)`: This is an overloaded function. Convenience method for inserting the image with the given `name` at the current `position()`.

``` cpp
QImage img = ...;
textDocument->addResource ( QTextDocument::ImageResource, QUrl ( "myimage" ), img );
cursor.insertImage ( "myimage" );
```

- `void QTextCursor::insertImage(const QImage & image, const QString & name = QString())`: This is an overloaded function. Convenience function for inserting the given `image` with an optional `name` at the current `position()`.
- `QTextList * QTextCursor::insertList(const QTextListFormat & format)`: Inserts a new block at the current position and makes it the first list item of a newly created list with the given `format`. Returns the created list.
- `QTextList * QTextCursor::insertList(QTextListFormat::Style style)`: This is an overloaded function. Inserts a new block at the current position and makes it the first list item of a newly created list with the given `style`. Returns the created list.
- `QTextTable * QTextCursor::insertTable(int rows, int columns, const QTextTableFormat & format)`: Creates a new table with the given number of `rows` and `columns` in the specified `format`, inserts it at the current cursor `position()` in the document, and returns the table object. The cursor is moved to the beginning of the first cell. There must be at least one row and one column in the table.
- `QTextTable * QTextCursor::insertTable(int rows, int columns)`: This is an overloaded function. Creates a new table with the given number of `rows` and `columns`, inserts it at the current cursor `position()` in the document, and returns the table object. The cursor is moved to the beginning of the first cell. There must be at least one row and one column in the table.
- `void QTextCursor::insertText(const QString & text)`: Inserts `text` at the current position, using the current character format. If there is a selection, the selection is deleted and replaced by `text`, for example:

``` cpp
cursor.clearSelection();
cursor.movePosition ( QTextCursor::NextWord, QTextCursor::KeepAnchor );
cursor.insertText ( "Hello World" );
```

This clears any existing selection, selects the word at the cursor (i.e. from `position()` forward), and replaces the selection with the phrase `Hello World`. Any `ASCII` linefeed characters (`\n`) in the inserted text are transformed into unicode block separators, corresponding to `insertBlock()` calls.

- `void QTextCursor::insertText(const QString & text, const QTextCharFormat & format)`: This is an overloaded function. Inserts `text` at the current position with the given `format`.
- `bool QTextCursor::isCopyOf(const QTextCursor & other) const`: Returns `true` if this cursor and `other` are copies of each other, i.e. one of them was created as a copy of the `other` and neither has moved since. This is much stricter than equality.
- `bool QTextCursor::isNull() const`: Returns `true` if the cursor is null; otherwise returns `false`. A null cursor is created by the default constructor.
- `void QTextCursor::joinPreviousEditBlock()`: Like `beginEditBlock()` indicates the start of a block of editing operations that should appear as a single operation for undo/redo. However unlike `beginEditBlock()` it does not start a new block but reverses the previous call to `endEditBlock()` and therefore makes following operations part of the previous edit block created. For example:

``` cpp
QTextCursor cursor ( textDocument );
cursor.beginEditBlock();
cursor.insertText ( "Hello" );
cursor.insertText ( "World" );
cursor.endEditBlock();
...
cursor.joinPreviousEditBlock();
cursor.insertText ( "Hey" );
cursor.endEditBlock();
​
textDocument->undo();
```

The call to `undo()` will cause all three insertions to be undone.

- `bool QTextCursor::keepPositionOnInsert() const`: Returns whether the cursor should keep its current position when text gets inserted at the position of the cursor. The default is `false`.
- `void QTextCursor::mergeBlockCharFormat(const QTextCharFormat & modifier)`: Modifies the block char format of the current block (or all blocks that are contained in the selection) with the block format specified by `modifier`.
- `void QTextCursor::mergeBlockFormat(const QTextBlockFormat & modifier)`: Modifies the block format of the current block (or all blocks that are contained in the selection) with the block format specified by `modifier`.
- `void QTextCursor::mergeCharFormat(const QTextCharFormat & modifier)`: Merges the cursor's current character format with the properties described by format `modifier`. If the cursor has a selection, this function applies all the properties set in `modifier` to all the character formats that are part of the selection.
- `bool QTextCursor::movePosition(MoveOperation operation, MoveMode mode = MoveAnchor, int n = 1)`: Moves the cursor by performing the given `operation` `n` times, using the specified `mode`, and returns `true` if all operations were completed successfully; otherwise returns `false`. For example, if this function is repeatedly used to seek to the end of the next word, it will eventually fail when the end of the document is reached. By default, the move `operation` is performed once (`n = 1`). If `mode` is `KeepAnchor`, the cursor selects the text it moves over. This is the same effect that the user achieves when they hold down the `Shift` key and move the cursor with the cursor keys.
- `int QTextCursor::position() const`: Returns the absolute position of the cursor within the document. The cursor is positioned between characters.
- `int QTextCursor::positionInBlock() const`: Returns the relative position of the cursor within the block. The cursor is positioned between characters.
- `void QTextCursor::removeSelectedText()`: If there is a selection, its content is deleted; otherwise does nothing.
- `void QTextCursor::select(SelectionType selection)`: Selects text in the document according to the given `selection`.
- `void QTextCursor::selectedTableCells(int * firstRow, int * numRows, int * firstColumn, int * numColumns) const`: If the selection spans over table cells, `firstRow` is populated with the number of the first row in the selection, `firstColumn` with the number of the first column in the selection, and `numRows` and `numColumns` with the number of rows and columns in the selection. If the selection does not span any table cells the results are harmless but undefined.
- `QString QTextCursor::selectedText() const`: Returns the current selection's text (which may be empty). This only returns the text, with no rich text formatting information. If you want a document fragment (i.e. formatted rich text) use `selection()` instead. **Note**: If the selection obtained from an editor spans a line break, the text will contain a `Unicode U+2029` paragraph separator character instead of a newline `\n` character. Use `QString::replace()` to replace these characters with newlines.
- `QTextDocumentFragment QTextCursor::selection() const`: Returns the current selection (which may be empty) with all its formatting information. If you just want the selected text (i.e. plain text) use `selectedText()` instead. **Note**: Unlike `QTextDocumentFragment::toPlainText()`, `selectedText()` may include special unicode characters such as `QChar::ParagraphSeparator`.
- `int QTextCursor::selectionEnd() const`: Returns the end of the selection or `position()` if the cursor doesn't have a selection.
- `int QTextCursor::selectionStart() const`: Returns the start of the selection or `position()` if the cursor doesn't have a selection.
- `void QTextCursor::setBlockCharFormat(const QTextCharFormat & format)`: Sets the block char format of the current block (or all blocks that are contained in the selection) to `format`.
- `void QTextCursor::setBlockFormat(const QTextBlockFormat & format)`: Sets the block format of the current block (or all blocks that are contained in the selection) to `format`.
- `void QTextCursor::setCharFormat(const QTextCharFormat & format)`: Sets the cursor's current character format to the given `format`. If the cursor has a selection, the given `format` is applied to the current selection.
- `void QTextCursor::setKeepPositionOnInsert(bool b)`: Defines whether the cursor should keep its current position when text gets inserted at the current position of the cursor. If `b` is `true`, the cursor keeps its current position when text gets inserted at the positing of the cursor. If `b` is `false`, the cursor moves along with the inserted text. The default is `false`. Note that a cursor always moves when text is inserted before the current position of the cursor, and it always keeps its position when text is inserted after the current position of the cursor.
- `void QTextCursor::setPosition(int pos, MoveMode m = MoveAnchor)`: Moves the cursor to the absolute position in the document specified by `pos` using a `MoveMode` specified by `m`. The cursor is positioned between characters.
- `void QTextCursor::setVerticalMovementX(int x)`: Sets the visual `x` position for vertical cursor movements to `x`. The vertical movement `x` position is cleared automatically when the cursor moves horizontally, and kept unchanged when the cursor moves vertically. The mechanism allows the cursor to move up and down on a visually straight line with proportional fonts, and to gently jump over short lines. A value of `-1` indicates no predefined `x` position. It will then be set automatically the next time the cursor moves up or down.
- `void QTextCursor::setVisualNavigation(bool b)`: Sets visual navigation to `b`. Visual navigation means skipping over hidden text pragraphs. The default is `false`.
- `int QTextCursor::verticalMovementX() const`: Returns the visual `x` position for vertical cursor movements. A value of `-1` indicates no predefined `x` position. It will then be set automatically the next time the cursor moves up or down.
- `bool QTextCursor::visualNavigation() const`: Returns `true` if the cursor does visual navigation; otherwise returns `false`. Visual navigation means skipping over hidden text pragraphs. The default is `false`.
- `bool QTextCursor::operator!=(const QTextCursor & other) const`: Returns `true` if the `other` cursor is at a different position in the document as this cursor; otherwise returns `false`.
- `bool QTextCursor::operator<(const QTextCursor & other) const`: Returns `true` if the `other` cursor is positioned later in the document than this cursor; otherwise returns `false`.
- `bool QTextCursor::operator<=(const QTextCursor & other) const`: Returns `true` if the `other` cursor is positioned later or at the same position in the document as this cursor; otherwise returns `false`.
- `QTextCursor & QTextCursor::operator=(const QTextCursor & cursor)`: Makes a copy of `cursor` and assigns it to this `QTextCursor`. Note that `QTextCursor` is an implicitly shared class.
- `bool QTextCursor::operator==(const QTextCursor & other) const`: Returns `true` if the `other` cursor is at the same position in the document as this cursor; otherwise returns `false`.
- `bool QTextCursor::operator>(const QTextCursor & other) const`: Returns `true` if the `other` cursor is positioned earlier in the document than this cursor; otherwise returns `false`.
- `bool QTextCursor::operator>=(const QTextCursor & other) const`: Returns `true` if the `other` cursor is positioned earlier or at the same position in the document as this cursor; otherwise returns `false`.