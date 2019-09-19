---
title: Qt之QTextFrame
date: 2019-01-02 21:05:02
categories: Qt语法详解
---
&emsp;&emsp;The `QTextFrame` class represents a frame in a `QTextDocument`.

Header       | Inherits      | Inherited By
-------------|---------------|--------------
`QTextFrame` | `QTextObject` | `QTextTable`

**Note**: All functions in this class are reentrant.

### Public Types

- `class`: iterator
- `typedef`: Iterator

### Public Functions

Return                | Function
----------------------|-------------
                      | `QTextFrame(QTextDocument * document)`
                      | `~QTextFrame()`
`iterator`            | `begin() const`
`QList<QTextFrame *>` | `childFrames() const`
`iterator`            | `end() const`
`QTextCursor`         | `firstCursorPosition() const`
`int`                 | `firstPosition() const`
`QTextFrameFormat`    | `frameFormat() const`
`QTextCursor`         | `lastCursorPosition() const`
`int`                 | `lastPosition() const`
`QTextFrame *`        | `parentFrame() const`
`void`                | `setFrameFormat(const QTextFrameFormat & format)`

### Detailed Description

&emsp;&emsp;The `QTextFrame` class represents a frame in a `QTextDocument`.
&emsp;&emsp;Text frames provide structure for the text in a document. They are used as generic containers for other document elements. Frames are usually created by using `QTextCursor::insertFrame()`.
&emsp;&emsp;Frames can be used to create hierarchical structures in rich text documents. Each document has a root frame (`QTextDocument::rootFrame()`), and each frame beneath the root frame has a parent frame and a (possibly empty) list of child frames. The parent frame can be found with `parentFrame()`, and the `childFrames()` function provides a list of child frames.
&emsp;&emsp;Each frame contains at least one text block to enable text cursors to insert new document elements within. As a result, the `QTextFrame::iterator` class is used to traverse both the blocks and child frames within a given frame. The first and last child elements in the frame can be found with `begin()` and `end()`.
&emsp;&emsp;A frame also has a format (specified using `QTextFrameFormat`) which can be set with `setFormat()` and read with `format()`.
&emsp;&emsp;Text cursors can be obtained that point to the first and last valid cursor positions within a frame; use the `firstCursorPosition()` and `lastCursorPosition()` functions for this. The frame's extent in the document can be found with `firstPosition()` and `lastPosition()`.
&emsp;&emsp;You can iterate over a frame's contents using the `QTextFrame::iterator` class: this provides `read-only` access to its internal list of text blocks and child frames.

### Member Type Documentation

- `typedef QTextFrame::Iterator`: `Qt-style` synonym for `QTextFrame::iterator`.

### Member Function Documentation

- `QTextFrame::QTextFrame(QTextDocument * document)`: Creates a new empty frame for the text `document`.
- `QTextFrame::~QTextFrame()`: Destroys the frame, and removes it from the document's layout.
- `iterator QTextFrame::begin() const`: Returns an iterator pointing to the `first` document element inside the frame. Please see the document `STL-style-Iterators` for more information.
- `QList<QTextFrame *> QTextFrame::childFrames() const`: Returns a (possibly empty) list of the frame's child frames.
- `iterator QTextFrame::end() const`: Returns an iterator pointing to the position past the `last` document element inside the frame. Please see the document `STL-Style` Iterators for more information.
- `QTextCursor QTextFrame::firstCursorPosition() const`: Returns the first cursor position inside the frame.
- `int QTextFrame::firstPosition() const`: Returns the first document position inside the frame.
- `QTextFrameFormat QTextFrame::frameFormat() const`: Returns the frame's format.
- `QTextCursor QTextFrame::lastCursorPosition() const`: Returns the last cursor position inside the frame.
- `int QTextFrame::lastPosition() const`: Returns the last document position inside the frame.
- `QTextFrame * QTextFrame::parentFrame() const`: Returns the frame's parent frame. If the frame is the root frame of a document, this will return `0`.
- `void QTextFrame::setFrameFormat(const QTextFrameFormat & format)`: Sets the frame's `format`.