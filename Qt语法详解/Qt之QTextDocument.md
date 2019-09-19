---
title: Qt之QTextDocument
date: 2019-02-02 00:28:55
categories: Qt语法详解
---
&emsp;&emsp;The `QTextDocument` class holds formatted text that can be viewed and edited using a `QTextEdit`.

Header          | Inherits
----------------|----------
`QTextDocument` | `QObject`

**Note**: All functions in this class are reentrant.

### Public Functions

Return                          | Function
--------------------------------|---------
                                | `QTextDocument(QObject * parent = 0)`
                                | `QTextDocument(const QString & text, QObject * parent = 0)`
                                | `~QTextDocument()`
`void`                          | `addResource(int type, const QUrl & name, const QVariant & resource)`
`void`                          | `adjustSize()`
`QVector<QTextFormat>`          | `allFormats() const`
`int`                           | `availableRedoSteps() const`
`int`                           | `availableUndoSteps() const`
`QTextBlock`                    | `begin() const`
`int`                           | `blockCount() const`
`QChar`                         | `characterAt(int pos) const`
`int`                           | `characterCount() const`
`virtual void`                  | `clear()`
`void`                          | `clearUndoRedoStacks(Stacks stacksToClear = UndoAndRedoStacks)`
`QTextDocument *`               | `clone(QObject * parent = 0) const`
`Qt::CursorMoveStyle`           | `defaultCursorMoveStyle() const`
`QFont`                         | `defaultFont() const`
`QString`                       | `defaultStyleSheet() const`
`QTextOption`                   | `defaultTextOption() const`
`QAbstractTextDocumentLayout *` | `documentLayout() const`
`qreal`                         | `documentMargin() const`
`void`                          | `drawContents(QPainter * p, const QRectF & rect = QRectF())`
`QTextBlock`                    | `end() const`
`QTextCursor`                   | `find(const QString & subString, const QTextCursor & cursor, FindFlags options = 0) const`
`QTextCursor`                   | `find(const QRegExp & expr, const QTextCursor & cursor, FindFlags options = 0) const`
`QTextCursor`                   | `find(const QString & subString, int position = 0, FindFlags options = 0) const`
`QTextCursor`                   | `find(const QRegExp & expr, int position = 0, FindFlags options = 0) const`
`QTextBlock`                    | `findBlock(int pos) const`
`QTextBlock`                    | `findBlockByLineNumber(int lineNumber) const`
`QTextBlock`                    | `findBlockByNumber(int blockNumber) const`
`QTextBlock`                    | `firstBlock() const`
`qreal`                         | `idealWidth() const`
`qreal`                         | `indentWidth() const`
`bool`                          | `isEmpty() const`
`bool`                          | `isModified() const`
`bool`                          | `isRedoAvailable() const`
`bool`                          | `isUndoAvailable() const`
`bool`                          | `isUndoRedoEnabled() const`
`QTextBlock`                    | `lastBlock() const`
`int`                           | `lineCount() const`
`void`                          | `markContentsDirty(int position, int length)`
`int`                           | `maximumBlockCount() const`
`QString`                       | `metaInformation(MetaInformation info) const`
`QTextObject *`                 | `object(int objectIndex) const`
`QTextObject *`                 | `objectForFormat(const QTextFormat & f) const`
`int`                           | `pageCount() const`
`QSizeF`                        | `pageSize() const`
`void`                          | `print(QPrinter * printer) const`
`void`                          | `redo(QTextCursor * cursor)`
`QVariant`                      | `resource(int type, const QUrl & name) const`
`int`                           | `revision() const`
`QTextFrame *`                  | `rootFrame() const`
`void`                          | `setDefaultCursorMoveStyle(Qt::CursorMoveStyle style)`
`void`                          | `setDefaultFont(const QFont & font)`
`void`                          | `setDefaultStyleSheet(const QString & sheet)`
`void`                          | `setDefaultTextOption(const QTextOption & option)`
`void`                          | `setDocumentLayout(QAbstractTextDocumentLayout * layout)`
`void`                          | `setDocumentMargin(qreal margin)`
`void`                          | `setHtml(const QString & html)`
`void`                          | `setIndentWidth(qreal width)`
`void`                          | `setMaximumBlockCount(int maximum)`
`void`                          | `setMetaInformation(MetaInformation info, const QString & string)`
`void`                          | `setPageSize(const QSizeF & size)`
`void`                          | `setPlainText(const QString & text)`
`void`                          | `setTextWidth(qreal width)`
`void`                          | `setUndoRedoEnabled(bool enable)`
`void`                          | `setUseDesignMetrics(bool b)`
`QSizeF`                        | `size() const`
`qreal`                         | `textWidth() const`
`QString`                       | `toHtml(const QByteArray & encoding = QByteArray()) const`
`QString`                       | `toPlainText() const`
`void`                          | `undo(QTextCursor * cursor)`
`bool`                          | `useDesignMetrics() const`

### Public Slots

Return | Function
-------|---------
`void` | `redo()`
`void` | `setModified(bool m = true)`
`void` | `undo()`

### Signals

Return | Function
-------|---------
`void` | `blockCountChanged(int newBlockCount)`
`void` | `contentsChange(int position, int charsRemoved, int charsAdded)`
`void` | `contentsChanged()`
`void` | `cursorPositionChanged(const QTextCursor & cursor)`
`void` | `documentLayoutChanged()`
`void` | `modificationChanged(bool changed)`
`void` | `redoAvailable(bool available)`
`void` | `undoAvailable(bool available)`
`void` | `undoCommandAdded()`

### Protected Functions

Retrun                  | Function
------------------------|---------
`virtual QTextObject *` | `createObject(const QTextFormat & format)`
`virtual QVariant`      | `loadResource(int type, const QUrl & name)`

### Detailed Description

&emsp;&emsp;The `QTextDocument` class holds formatted text that can be viewed and edited using a `QTextEdit`.
&emsp;&emsp;`QTextDocument` is a container for structured rich text documents, providing support for styled text and various types of document elements, such as lists, tables, frames, and images. They can be created for use in a `QTextEdit`, or used independently.
&emsp;&emsp;Each document element is described by an associated format object. Each format object is treated as a unique object by `QTextDocuments`, and can be passed to `objectForFormat()` to obtain the document element that it is applied to.
&emsp;&emsp;A `QTextDocument` can be edited programmatically using a `QTextCursor`, and its contents can be examined by traversing the document structure. The entire document structure is stored as a hierarchy of document elements beneath the root frame, found with the `rootFrame()` function. Alternatively, if you just want to iterate over the textual contents of the document you can use `begin()`, `end()` and `findBlock()` to retrieve text blocks that you can examine and iterate over.
&emsp;&emsp;The layout of a document is determined by the `documentLayout()`; you can create your own `QAbstractTextDocumentLayout` subclass and set it using `setDocumentLayout()` if you want to use your own layout logic. The document's title and other `meta-information` can be obtained by calling the `metaInformation()` function. For documents that are exposed to users through the `QTextEdit` class, the document title is also available via the `QTextEdit::documentTitle()` function.
&emsp;&emsp;The `toPlainText()` and `toHtml()` convenience functions allow you to retrieve the contents of the document as `plain text` and `HTML`. The document's text can be searched using the `find()` functions.
&emsp;&emsp;Undo/redo of operations performed on the document can be controlled using the `setUndoRedoEnabled()` function. The undo/redo system can be controlled by an editor widget through the `undo()` and `redo()` slots; the document also provides `contentsChanged()`, `undoAvailable()` and `redoAvailable()` signals that inform connected editor widgets about the state of the undo/redo system. The following are the undo/redo operations of a `QTextDocument`:

- Insertion or removal of characters. A sequence of insertions or removals within the same text block are regarded as a single undo/redo operation.
- Insertion or removal of text blocks. Sequences of insertion or removals in a single operation (e.g., by selecting and then deleting text) are regarded as a single undo/redo operation.
- Text character format changes.
- Text block format changes.
- Text block group format changes.

### Member Type Documentation

- enum `QTextDocument::FindFlag & flags QTextDocument::FindFlags`: This enum describes the options available to `QTextDocument's` find function. The options can be `OR-ed` together from the following list:

Constant                             | Value     | Description
-------------------------------------|-----------|------------
`QTextDocument::FindBackward`        | `0x00001` | Search backwards instead of forwards.
`QTextDocument::FindCaseSensitively` | `0x00002` | By default find works case insensitive. Specifying this option changes the behaviour to a case sensitive find operation.
`QTextDocument::FindWholeWords`      | `0x00004` | Makes find match only complete words.

The FindFlags type is a typedef for `QFlags<FindFlag>`. It stores an `OR` combination of `FindFlag` values.

- enum `QTextDocument::MetaInformation`: This enum describes the different types of meta information that can be added to a document.

Constant                       | Value | Description
-------------------------------|-------|------------
`QTextDocument::DocumentTitle` | `0`   | The title of the document.
`QTextDocument::DocumentUrl`   | `1`   | The url of the document. The `loadResource()` function uses this url as the base when loading relative resources.

- enum `QTextDocument::ResourceType`: This enum describes the types of resources that can be loaded by `QTextDocument's` `loadResource()` function.

Constant                            | Value | Description
------------------------------------|-------|------------
`QTextDocument::HtmlResource`       | `1`   | The resource contains `HTML`.
`QTextDocument::ImageResource`      | `2`   | The resource contains image data. Currently supported data types are `QVariant::Pixmap` and `QVariant::Image`. If the corresponding variant is of type `QVariant::ByteArray` then Qt attempts to load the image using `QImage::loadFromData`. `QVariant::Icon` is currently not supported. The icon needs to be converted to one of the supported types first, for example using `QIcon::pixmap`.
`QTextDocument::StyleSheetResource` | `3`   | The resource contains `CSS`.
`QTextDocument::UserResource`       | `100` | The first available value for user defined resource types.

- enum `QTextDocument::Stacks`:

Constant                           | Value                                   | Description
-----------------------------------|-----------------------------------------|-------------
`QTextDocument::UndoStack`         | `0x01`                                  | The undo stack.
`QTextDocument::RedoStack`         | `0x02`                                  | The redo stack.
`QTextDocument::UndoAndRedoStacks` | <code>UndoStack &#124; RedoStack</code> | Both the undo and redo stacks.

### Property Documentation

- `blockCount(const int)`: Returns the number of text blocks in the document. The value of this property is undefined in documents with tables or frames. By default, if defined, this property contains a value of `1`. Access functions:

``` cpp
int blockCount() const
```

- `defaultFont(QFont)`: This property holds the default font used to display the document's text. Access functions:

``` cpp
QFont defaultFont() const
void setDefaultFont ( const QFont &font )
```

- `defaultStyleSheet(QString)`: The default style sheet is applied to all newly `HTML` formatted text that is inserted into the document, for example using `setHtml()` or `QTextCursor::insertHtml()`. The style sheet needs to be compliant to `CSS 2.1` syntax. **Note**: Changing the default style sheet does not have any effect to the existing content of the document. Access functions:

``` cpp
QString defaultStyleSheet() const
void setDefaultStyleSheet ( const QString &sheet )
```

- `defaultTextOption(QTextOption)`: This property holds the default text option will be set on all `QTextLayouts` in the document. When `QTextBlocks` are created, the `defaultTextOption` is set on their `QTextLayout`. This allows setting global properties for the document such as the default word wrap mode. Access functions:

``` cpp
QTextOption defaultTextOption() const
void setDefaultTextOption ( const QTextOption &option )
```

- `documentMargin(qreal)`: The margin around the document. The default is `4`. Access functions:

``` cpp
qreal documentMargin() const
void setDocumentMargin ( qreal margin )
```

`indentWidth(qreal)`: Returns the width used for text list and text block indenting. The indent properties of `QTextListFormat` and `QTextBlockFormat` specify multiples of this value. The default indent width is `40`. Access functions:

``` cpp
qreal indentWidth() const
void setIndentWidth ( qreal width )
```

- `maximumBlockCount(int)`: This property specifies the limit for blocks in the document. Specifies the maximum number of blocks the document may have. If there are more blocks in the document that specified with this property blocks are removed from the beginning of the document. A negative or zero value specifies that the document may contain an unlimited amount of blocks. The default value is `0`. Note that setting this property will apply the limit immediately to the document contents. Setting this property also disables the undo redo history. This property is undefined in documents with tables or frames. Access functions:

``` cpp
int maximumBlockCount() const
void setMaximumBlockCount ( int maximum )
```

- `modified(bool)`: This property holds whether the document has been modified by the user. By default, this property is `false`. Access functions:

``` cpp
bool isModified() const
void setModified ( bool m = true )
```

- `pageSize(QSizeF)`: This property holds the page size that should be used for laying out the document. By default, for a `newly-created`, empty document, this property contains an undefined size. Access functions:

``` cpp
QSizeF pageSize() const
void setPageSize(const QSizeF & size)
```

- `size(const QSizeF)`: Returns the actual size of the document. This is equivalent to `documentLayout()->documentSize()`; The size of the document can be changed either by setting a text width or setting an entire page size. Note that the width is always `>= pageSize().width()`. By default, for a `newly-created`, empty document, this property contains a `configuration-dependent` size. Access functions:

``` cpp
QSizeF size() const
```

- `textWidth(qreal)`: The text width specifies the preferred width for text in the document. If the text (or content in general) is wider than the specified with it is broken into multiple lines and grows vertically. If the text cannot be broken into multiple lines to fit into the specified text width it will be larger and the `size()` and the `idealWidth()` property will reflect that. If the text width is set to `-1` then the text will not be broken into multiple lines unless it is enforced through an explicit line break or a new paragraph. The default value is `-1`. Setting the text width will also set the page height to `-1`, causing the document to grow or shrink vertically in a continuous way. If you want the document layout to break the text into multiple pages then you have to set the pageSize property instead. Access functions:

``` cpp
qreal textWidth() const
void setTextWidth ( qreal width )
```

- `undoRedoEnabled(bool)`: This property holds whether undo/redo are enabled for this document. This defaults to `true`. If disabled, the undo stack is cleared and no items will be added to it. Access functions:

``` cpp
bool isUndoRedoEnabled() const
void setUndoRedoEnabled ( bool enable )
```

- `useDesignMetrics(bool)`: This property holds whether the document uses design metrics of fonts to improve the accuracy of text layout. If this property is set to `true`, the layout will use design metrics. Otherwise, the metrics of the paint device as set on `QAbstractTextDocumentLayout::setPaintDevice()` will be used. Using design metrics makes a layout have a width that is no longer dependent on hinting and `pixel-rounding`. This means that `WYSIWYG` text layout becomes possible because the width scales much more linearly based on paintdevice metrics than it would otherwise. By default, this property is `false`. Access functions:

``` cpp
bool useDesignMetrics() const
void setUseDesignMetrics ( bool b )
```

### Member Function Documentation

- `QTextDocument::QTextDocument(QObject * parent = 0)`: Constructs an empty `QTextDocument` with the given `parent`.
- `QTextDocument::QTextDocument(const QString & text, QObject * parent = 0)`: Constructs a `QTextDocument` containing the plain (unformatted) `text` specified, and with the given `parent`.
- `QTextDocument::~QTextDocument()`: Destroys the document.
- `void QTextDocument::addResource(int type, const QUrl & name, const QVariant & resource)`: Adds the `resource` to the resource cache, using `type` and `name` as identifiers. `type` should be a value from `QTextDocument::ResourceType`. For example, you can add an image as a resource in order to reference it from within the document:

``` cpp
document->addResource ( QTextDocument::ImageResource, QUrl ( "mydata://image.png" ), QVariant ( image ) );
```

The image can be inserted into the document using the `QTextCursor`:

``` cpp
QTextImageFormat imageFormat;
imageFormat.setName ( "mydata://image.png" );
cursor.insertImage ( imageFormat );
```

Alternatively, you can insert images using the `HTML` img tag:

``` cpp
editor->append ( "<img src=\"mydata://image.png\" />" );
```

- `void QTextDocument::adjustSize()`: Adjusts the document to a reasonable size.
- `QVector<QTextFormat> QTextDocument::allFormats() const`: Returns a vector of text formats for all the formats used in the document.
- `int QTextDocument::availableRedoSteps() const`: Returns the number of available redo steps.
- `int QTextDocument::availableUndoSteps() const`: Returns the number of available undo steps.
- `QTextBlock QTextDocument::begin() const`: Returns the document's first text block.
- `void QTextDocument::blockCountChanged(int newBlockCount) [signal]`: This signal is emitted when the total number of text blocks in the document changes. The value passed in `newBlockCount` is the new total.
- `QChar QTextDocument::characterAt(int pos) const`: Returns the character at position `pos`, or a null character if the position is out of range.
- `int QTextDocument::characterCount() const`: Returns the number of characters of this document.
- `void QTextDocument::clear() [virtual]`: Clears the document.
- `void QTextDocument::clearUndoRedoStacks(Stacks stacksToClear = UndoAndRedoStacks)`: Clears the stacks specified by `stacksToClear`. This method clears any commands on the undo stack, the redo stack, or both (the default). If commands are cleared, the appropriate signals are emitted, `QTextDocument::undoAvailable()` or `QTextDocument::redoAvailable()`.
- `QTextDocument * QTextDocument::clone(QObject * parent = 0) const`: Creates a new QTextDocument that is a copy of this text document. `parent` is the parent of the returned text document.
- `void QTextDocument::contentsChange(int position, int charsRemoved, int charsAdded) [signal]`: This signal is emitted whenever the document's content changes; for example, when text is inserted or deleted, or when formatting is applied. Information is provided about the `position` of the character in the document where the change occurred, the number of characters removed (`charsRemoved`), and the number of characters added (`charsAdded`). The signal is emitted before the document's layout manager is notified about the change. This hook allows you to implement syntax highlighting for the document.
- `void QTextDocument::contentsChanged() [signal]`: This signal is emitted whenever the document's content changes; for example, when text is inserted or deleted, or when formatting is applied.
- `QTextObject * QTextDocument::createObject(const QTextFormat & format) [virtual protected]`: Creates and returns a new document object (a `QTextObject`), based on the given `format`. `QTextObjects` will always get created through this method, so you must reimplement it if you use custom text objects inside your document.
- `void QTextDocument::cursorPositionChanged(const QTextCursor & cursor) [signal]`: This signal is emitted whenever the position of a cursor changed due to an editing operation. The cursor that changed is passed in `cursor`. If you need a signal when the cursor is moved with the arrow keys, you can use the `cursorPositionChanged()` signal in `QTextEdit`.
- `Qt::CursorMoveStyle QTextDocument::defaultCursorMoveStyle() const`: The default cursor movement style is used by all `QTextCursor` objects created from the document. The default is `Qt::LogicalMoveStyle`.
- `QAbstractTextDocumentLayout * QTextDocument::documentLayout() const`: Returns the document layout for this document.
- `void QTextDocument::documentLayoutChanged() [signal]`: This signal is emitted when a new document layout is set.
- `void QTextDocument::drawContents(QPainter * p, const QRectF & rect = QRectF())`: Draws the content of the document with painter `p`, clipped to `rect`. If `rect` is a null rectangle (default), then the document is painted unclipped.
- `QTextBlock QTextDocument::end() const`: This function returns a block to test for the end of the document while iterating over it.

``` cpp
for ( QTextBlock it = doc->begin(); it != doc->end(); it = it.next() ) {
    cout << it.text().toStdString() << endl;
}
```

The block returned is invalid and represents the block after the last block in the document. You can use `lastBlock()` to retrieve the last valid block of the document.

- `QTextCursor QTextDocument::find(const QString & subString, const QTextCursor & cursor, FindFlags options = 0) const`: Finds the next occurrence of the string, `subString`, in the document. The search starts at the position of the given `cursor`, and proceeds forwards through the document unless specified otherwise in the search options. The `options` control the type of search performed. Returns a cursor with the match selected if `subString` was found; otherwise returns a null cursor. If the given `cursor` has a selection, the search begins after the selection; otherwise it begins at the cursor's position. By default the search is `case-sensitive`, and can match text anywhere in the document.
- `QTextCursor QTextDocument::find(const QRegExp & expr, const QTextCursor & cursor, FindFlags options = 0) const`: Finds the next occurrence, matching the regular expression, `expr`, in the document. The search starts at the position of the given `cursor`, and proceeds forwards through the document unless specified otherwise in the search options. The `options` control the type of search performed. The `FindCaseSensitively` option is ignored for this overload, use `QRegExp::caseSensitivity` instead. Returns a cursor with the match selected if a match was found; otherwise returns a null cursor. If the given `cursor` has a selection, the search begins after the selection; otherwise it begins at the cursor's position. By default the search is `case-sensitive`, and can match text anywhere in the document.
- `QTextCursor QTextDocument::find(const QString & subString, int position = 0, FindFlags options = 0) const`: This is an overloaded function. Finds the next occurrence of the string, `subString`, in the document. The search starts at the given `position`, and proceeds forwards through the document unless specified otherwise in the search options. The `options` control the type of search performed. Returns a cursor with the match selected if `subString` was found; otherwise returns a null cursor. If the `position` is `0` (the default), the search begins from the beginning of the document; otherwise it begins at the specified `position`.
- `QTextCursor QTextDocument::find(const QRegExp & expr, int position = 0, FindFlags options = 0) const`: This is an overloaded function. Finds the next occurrence, matching the regular expression, `expr`, in the document. The search starts at the given `position`, and proceeds forwards through the document unless specified otherwise in the search options. The `options` control the type of search performed. The `FindCaseSensitively` option is ignored for this overload, use `QRegExp::caseSensitivity` instead. Returns a cursor with the match selected if a match was found; otherwise returns a null cursor. If the `position` is `0` (the default) the search begins from the beginning of the document; otherwise it begins at the specified `position`.
- `QTextBlock QTextDocument::findBlock(int pos) const`: Returns the text block that contains the `pos-th` character.
- `QTextBlock QTextDocument::findBlockByLineNumber(int lineNumber) const`: Returns the text block that contains the specified `lineNumber`.
- `QTextBlock QTextDocument::findBlockByNumber(int blockNumber) const`: Returns the text block with the specified `blockNumber`.
- `QTextBlock QTextDocument::firstBlock() const`: Returns the document's first text block.
- `qreal QTextDocument::idealWidth() const`: Returns the ideal width of the text document. The ideal width is the actually used width of the document without optional alignments taken into account. It is always `<= size().width()`.
- `bool QTextDocument::isEmpty() const`: Returns `true` if the document is empty; otherwise returns `false`.
- `bool QTextDocument::isRedoAvailable() const`: Returns `true` if redo is available; otherwise returns `false`.
- `bool QTextDocument::isUndoAvailable() const`: Returns `true` if undo is available; otherwise returns `false`.
- `QTextBlock QTextDocument::lastBlock() const`: Returns the document's last (valid) text block.
- `int QTextDocument::lineCount() const`: Returns the number of lines of this document (if the layout supports this). Otherwise, this is identical to the number of blocks.
- `QVariant QTextDocument::loadResource(int type, const QUrl & name) [virtual protected]`: Loads data of the specified `type` from the resource with the given `name`. This function is called by the rich text engine to request data that isn't directly stored by `QTextDocument`, but still associated with it. For example, images are referenced indirectly by the name attribute of a `QTextImageFormat` object. When called by `Qt`, `type` is one of the values of `QTextDocument::ResourceType`. If the `QTextDocument` is a child object of a `QTextEdit`, `QTextBrowser`, or a `QTextDocument` itself then the default implementation tries to retrieve the data from the parent.
- `void QTextDocument::markContentsDirty(int position, int length)`: Marks the contents specified by the given `position` and `length` as dirty, informing the document that it needs to be laid out again.
- `QString QTextDocument::metaInformation(MetaInformation info) const`: Returns meta information about the document of the type specified by `info`.
- `void QTextDocument::modificationChanged(bool changed) [signal]`: This signal is emitted whenever the content of the document changes in a way that affects the modification state. If `changed` is `true`, the document has been modified; otherwise it is `false`. For example, calling `setModified(false)` on a document and then inserting text causes the signal to get emitted. If you undo that operation, causing the document to return to its original unmodified state, the signal will get emitted again.
- `QTextObject * QTextDocument::object(int objectIndex) const`: Returns the text object associated with the given `objectIndex`.
- `QTextObject * QTextDocument::objectForFormat(const QTextFormat & f) const`: Returns the text object associated with the format `f`.
- `int QTextDocument::pageCount() const`: Returns the number of pages in this document.
- `void QTextDocument::print(QPrinter * printer) const`: Prints the document to the given `printer`. The `QPrinter` must be set up before being used with this function. This is only a convenience method to print the whole document to the printer. If the document is already paginated through a specified height in the `pageSize()` property it is printed `as-is`. If the document is not paginated, like for example a document used in a `QTextEdit`, then a temporary copy of the document is created and the copy is broken into multiple pages according to the size of the `QPrinter's` `paperRect()`. By default a `2` cm margin is set around the document contents. In addition the current page number is printed at the bottom of each page. Note that `QPrinter::Selection` is not supported as print range with this function since the selection is a property of `QTextCursor`. If you have a `QTextEdit` associated with your `QTextDocument` then you can use `QTextEdit's` `print()` function because `QTextEdit` has access to the user's selection.
- `void QTextDocument::redo(QTextCursor * cursor)`: Redoes the last editing operation on the document if redo is available. The provided `cursor` is positioned at the end of the location where the edition operation was redone.
- `void QTextDocument::redo() [slot]`: This is an overloaded function. Redoes the last editing operation on the document if redo is available.
- `void QTextDocument::redoAvailable(bool available) [signal]`: This signal is emitted whenever redo operations become available (`available` is `true`) or unavailable (`available` is `false`).
- `QVariant QTextDocument::resource(int type, const QUrl & name) const`: Returns data of the specified `type` from the resource with the given `name`. This function is called by the rich text engine to request data that isn't directly stored by `QTextDocument`, but still associated with it. For example, images are referenced indirectly by the name attribute of a `QTextImageFormat` object. Resources are cached internally in the document. If a resource can not be found in the cache, `loadResource` is called to try to load the resource. `loadResource` should then use `addResource` to add the resource to the cache.
- `int QTextDocument::revision() const`: Returns the document's revision (if undo is enabled). The revision is guaranteed to increase when a document that is not modified is edited.
- `QTextFrame * QTextDocument::rootFrame() const`: Returns the document's root frame.
- `void QTextDocument::setDefaultCursorMoveStyle(Qt::CursorMoveStyle style)`: Sets the default cursor movement style to the given `style`.
- `void QTextDocument::setDocumentLayout(QAbstractTextDocumentLayout * layout)`: Sets the document to use the given `layout`. The previous layout is deleted.
- `void QTextDocument::setHtml(const QString & html)`: Replaces the entire contents of the document with the given `HTML-formatted` text in the `html` string. The `HTML` formatting is respected as much as possible; for example, `<b>bold</b> text` will produce text where the first word has a font weight that gives it a bold appearance: `bold text`. **Note**: It is the responsibility of the caller to make sure that the text is correctly decoded when a `QString` containing `HTML` is created and passed to `setHtml()`.
- `void QTextDocument::setMetaInformation(MetaInformation info, const QString & string)`: Sets the document's meta information of the type specified by `info` to the given `string`.
- `void QTextDocument::setPlainText(const QString & text)`: Replaces the entire contents of the document with the given plain `text`.
- `QString QTextDocument::toHtml(const QByteArray & encoding = QByteArray()) const`: Returns a string containing an `HTML` representation of the document. The `encoding` parameter specifies the value for the charset attribute in the html header. For example if `utf-8` is specified then the beginning of the generated html will look like this:

``` xml
<html><head><meta http-equiv="Content-Type" content="text/html; charset=utf-8"></head><body>...
```

If no `encoding` is specified then no such meta information is generated. If you later on convert the returned html string into a byte array for transmission over a network or when saving to disk you should specify the encoding you're going to use for the conversion to a byte array here.

- `QString QTextDocument::toPlainText() const`: Returns the plain text contained in the document. If you want formatting information use a `QTextCursor` instead.
- `void QTextDocument::undo(QTextCursor * cursor)`: Undoes the last editing operation on the document if undo is available. The provided `cursor` is positioned at the end of the location where the edition operation was undone.
- `void QTextDocument::undo() [slot]`: This is an overloaded function.
- `void QTextDocument::undoAvailable(bool available) [signal]`: This signal is emitted whenever undo operations become available (`available` is true) or unavailable (`available` is false).
- `void QTextDocument::undoCommandAdded() [signal]`: This signal is emitted every time a new level of undo is added to the `QTextDocument`.