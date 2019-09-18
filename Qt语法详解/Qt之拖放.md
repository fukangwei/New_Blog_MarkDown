---
title: Qt之拖放
date: 2019-02-01 18:27:48
categories: Qt语法详解
---
&emsp;&emsp;拖放是应用程序内或者多个应用程序之间传递信息的一种直观的操作方式。除了为剪贴板提供支持之外，通常还提供数据的移动和复制功能。
&emsp;&emsp;拖放操作包括两个截然不同的动作，即拖动、放下。`Qt`窗口部件可以作为拖动点(`drag site`)、放下点(`drop site`)或者同时作为拖动点和放下点。
&emsp;&emsp;下面介绍如何让一个`Qt`应用程序接收一个拖动操作，当用户从桌面或者文件资源管理器中拖动一个文件到这个应用程序上放下时，该应用程序就会将文件的信息显示出来。

``` cpp
class TabelView : public QTableView {
public:
    explicit TabelView ( QWidget *parent = 0 );
protected:
    void dragEnterEvent ( QDragEnterEvent *event );
    void dragMoveEvent ( QDragMoveEvent *event );
    void dropEvent ( QDropEvent *event );
};
​
TabelView::TabelView ( QWidget *parent ) : QTableView ( parent ) {
    setAcceptDrops ( true );
    setDragDropMode ( QAbstractItemView::DragDrop );
    setDragEnabled ( true );
    setDropIndicatorShown ( true );
    this->setWindowTitle ( "TableView" );
}
​
void TabelView::dragEnterEvent ( QDragEnterEvent *event ) {
    if ( event->mimeData()->hasFormat ( "text/uri-list" ) ) {
        event->acceptProposedAction();
    }
​
    qDebug() << "drag enter";
}
```

&emsp;&emsp;以上通过自定义`QTableView`来实现拖放事件，首先需要设置`setAcceptDrops(true)`来接受放下事件，通过设置`setDropIndicatorShown(true)`则可以清晰地看到放下过程中的图标指示。然后实现`dragEnterEvent`、`dropEvent`方法，当用户把一个对象拖动到这个窗体上时，就会调用`dragEnterEvent`，如果对这个事件调用`acceptProposedAction`，就表明可以在这个窗体上拖放对象。默认情况下窗口部件是不接受拖动的，`Qt`会自动改变光标向用户说明这个窗口部件不是有效的放下点。
&emsp;&emsp;我们希望用户拖放的只能是文件，而非其他类型的东西。为了实现这一点，可以检查拖动的`MIME`类型。`MIME`类型中`text/uri-list`用于存储统一资源标识符，它们可以是文件名、统一资源定位器(例如`HTTP`、`FTP`路径)或者其它全局资源标识符。标准的`MIME`类型由国际因特网地址分配委员会`IANA`定义的，它们由类型、子类型信息以及分割两者的斜线组成。`MIME`类通常由剪贴板和拖放系统使用，以识别不同类型的数据。
&emsp;&emsp;当用户在窗口部件上放下一个对象时，就会调用`dropEvent`。我们调用函数`QMimeData::urls`来获得`QUrl`列表。通常情况下，用户一次只拖动一个选择区域来同时拖动多个文件也是可能的，如果要拖放的`URL`不止一个，或者要拖放的`URL`不是一个本地文件名，则会立即返回到原调用处。

``` cpp
void TabelView::dragMoveEvent ( QDragMoveEvent *event ) {
    qDebug() << "drag move";
}
```

&emsp;&emsp;`QWidget`也提供了`dragMoveEvent`和`dragLeveEvent`函数，但是绝大多数情况下并不需要重新实现，上面简单实现了`dragMoveEvent`函数。

---

### Drag and Drop

&emsp;&emsp;Drag and drop provides a simple visual mechanism which users can use to transfer information between and within applications (In the literature this is referred to as a `direct manipulation model`). Drag and drop is similar in function to the clipboard's cut and paste mechanism.
&emsp;&emsp;This document describes the basic drag and drop mechanism and outlines the approach used to enable it in custom widgets. Drag and drop operations are also supported by `Qt's` item views and by the graphics view framework. More information is available in Using drag and drop with item views and `Graphics View Framework`.

### Drag and Drop Classes

&emsp;&emsp;These classes deal with drag and drop and the necessary mime type encoding and decoding.

- `QDragEnterEvent`: Event which is sent to a widget when a drag and drop action enters it.
- `QDragLeaveEvent`: Event that is sent to a widget when a drag and drop action leaves it.
- `QDragMoveEvent`: Event which is sent while a drag and drop action is in progress.
- `QDropEvent`: Event which is sent when a drag and drop action is completed.
- `QMacPasteboardMime`: Converts between a `MIME` type and a `Uniform Type Identifier` (`UTI`) format.
- `QWindowsMime`: Maps `open-standard` `MIME` to Window `Clipboard` formats.

### Configuration

&emsp;&emsp;The `QApplication` object provides some properties that are related to drag and drop operations:

- `QApplication::startDragTime` describes the amount of time in milliseconds that the user must hold down a mouse button over an object before a drag will begin.
- `QApplication::startDragDistance` indicates how far the user has to move the mouse while holding down a mouse button before the movement will be interpreted as dragging. Use of high values for this quantity prevents accidental dragging when the user only meant to click on an object.

&emsp;&emsp;These quantities provide sensible default values for you to use if you provide drag and drop support in your widgets.

### Dragging

&emsp;&emsp;To start a drag, create a `QDrag` object, and call its `exec()` function. In most applications, it is a good idea to begin a drag and drop operation only after a mouse button has been pressed and the cursor has been moved a certain distance. However, the simplest way to enable dragging from a widget is to reimplement the widget's `mousePressEvent()` and start a drag and drop operation:

``` cpp
void MainWindow::mousePressEvent ( QMouseEvent *event ) {
    if ( event->button() == Qt::LeftButton
         && iconLabel->geometry().contains ( event->pos() ) ) {
        QDrag *drag = new QDrag ( this );
        QMimeData *mimeData = new QMimeData;
        mimeData->setText ( commentEdit->toPlainText() );
        drag->setMimeData ( mimeData );
        drag->setPixmap ( iconPixmap );
        Qt::DropAction dropAction = drag->exec();
        ...
    }
}
```

&emsp;&emsp;Although the user may take some time to complete the dragging operation, as far as the application is concerned the `exec()` function is a blocking function that returns with one of several values. These indicate how the operation ended, and are described in more detail below.
&emsp;&emsp;Note that the `exec()` function does not block the main event loop.
&emsp;&emsp;For widgets that need to distinguish between mouse clicks and drags, it is useful to reimplement the widget's `mousePressEvent()` function to record to start position of the drag:

``` cpp
void DragWidget::mousePressEvent ( QMouseEvent *event ) {
    if ( event->button() == Qt::LeftButton ) {
        dragStartPosition = event->pos();
    }
}
```

&emsp;&emsp;Later, in `mouseMoveEvent()`, we can determine whether a drag should begin, and construct a drag object to handle the operation:

``` cpp
void DragWidget::mouseMoveEvent ( QMouseEvent *event ) {
    if ( ! ( event->buttons() & Qt::LeftButton ) ) {
        return;
    }
​
    if ( ( event->pos() - dragStartPosition ).manhattanLength() < QApplication::startDragDistance() ) {
        return;
    }
​
    QDrag *drag = new QDrag ( this );
    QMimeData *mimeData = new QMimeData;
    mimeData->setData ( mimeType, data );
    drag->setMimeData ( mimeData );
    Qt::DropAction dropAction = drag->exec ( Qt::CopyAction | Qt::MoveAction );
    ...
}
```

This particular approach uses the `QPoint::manhattanLength()` function to get a rough estimate of the distance between where the mouse click occurred and the current cursor position. This function trades accuracy for speed, and is usually suitable for this purpose.

### Dropping

&emsp;&emsp;To be able to receive media dropped on a widget, call `setAcceptDrops(true)` for the widget, and reimplement the `dragEnterEvent()` and `dropEvent()` event handler functions.
&emsp;&emsp;For example, the following code enables drop events in the constructor of a `QWidget` subclass, making it possible to usefully implement drop event handlers:

``` cpp
Window::Window ( QWidget *parent ) : QWidget ( parent ) {
    ...
    setAcceptDrops ( true );
}
```

&emsp;&emsp;The `dragEnterEvent()` function is typically used to inform `Qt` about the types of data that the widget accepts. You must reimplement this function if you want to receive either `QDragMoveEvent` or `QDropEvent` in your reimplementations of `dragMoveEvent()` and `dropEvent()`.
&emsp;&emsp;The following code shows how `dragEnterEvent()` can be reimplemented to tell the drag and drop system that we can only handle plain text:

``` cpp
void Window::dragEnterEvent ( QDragEnterEvent *event ) {
    if ( event->mimeData()->hasFormat ( "text/plain" ) ) {
        event->acceptProposedAction();
    }
}
```

&emsp;&emsp;The `dropEvent()` is used to unpack dropped data and handle it in way that is suitable for your application.
&emsp;&emsp;In the following code, the text supplied in the event is passed to a `QTextBrowser` and a `QComboBox` is filled with the list of `MIME` types that are used to describe the data:

``` cpp
void Window::dropEvent ( QDropEvent *event ) {
    textBrowser->setPlainText ( event->mimeData()->text() );
    mimeTypeCombo->clear();
    mimeTypeCombo->addItems ( event->mimeData()->formats() );
    event->acceptProposedAction();
}
```

&emsp;&emsp;In this case, we accept the proposed action without checking what it is. In a real world application, it may be necessary to return from the `dropEvent()` function without accepting the proposed action or handling the data if the action is not relevant. For example, we may choose to ignore `Qt::LinkAction` actions if we do not support links to external sources in our application.

### Overriding Proposed Actions

&emsp;&emsp;We may also ignore the proposed action, and perform some other action on the data. To do this, we would call the event object's `setDropAction()` with the preferred action from `Qt::DropAction` before calling `accept()`. This ensures that the replacement drop action is used instead of the proposed action.
&emsp;&emsp;For more sophisticated applications, reimplementing `dragMoveEvent()` and `dragLeaveEvent()` will let you make certain parts of your widgets sensitive to drop events, and give you more control over drag and drop in your application.

### Subclassing Complex Widgets

&emsp;&emsp;Certain standard `Qt` widgets provide their own support for drag and drop. When subclassing these widgets, it may be necessary to reimplement `dragMoveEvent()` in addition to `dragEnterEvent()` and `dropEvent()` to prevent the base class from providing default drag and drop handling, and to handle any special cases you are interested in.

### Drag and Drop Actions

&emsp;&emsp;In the simplest case, the target of a drag and drop action receives a copy of the data being dragged, and the source decides whether to delete the original. This is described by the `CopyAction` action. The target may also choose to handle other actions, specifically the `MoveAction` and `LinkAction` actions. If the source calls `QDrag::exec()`, and it returns `MoveAction`, the source is responsible for deleting any original data if it chooses to do so. The `QMimeData` and `QDrag` objects created by the source widget should not be deleted -- they will be destroyed by `Qt`. The target is responsible for taking ownership of the data sent in the drag and drop operation; this is usually done by keeping references to the data.
&emsp;&emsp;If the target understands the `LinkAction` action, it should store its own reference to the original information; the source does not need to perform any further processing on the data. The most common use of drag and drop actions is when performing a `Move` within the same widget.
&emsp;&emsp;The other major use of drag actions is when using a reference type such as `text/uri-list`, where the dragged data are actually references to files or objects.

### Adding New Drag and Drop Types

&emsp;&emsp;Drag and drop is not limited to text and images. Any type of information can be transferred in a drag and drop operation. To drag information between applications, the applications must be able to indicate to each other which data formats they can accept and which they can produce. This is achieved using `MIME` types. The `QDrag` object constructed by the source contains a list of `MIME` types that it uses to represent the data (ordered from most appropriate to least appropriate), and the drop target uses one of these to access the data. For common data types, the convenience functions handle the `MIME` types used transparently but, for custom data types, it is necessary to state them explicitly.
&emsp;&emsp;To implement drag and drop actions for a type of information that is not covered by the `QDrag` convenience functions, the first and most important step is to look for existing formats that are appropriate: The `Internet Assigned Numbers Authority` (`IANA`) provides a hierarchical list of `MIME` media types at the `Information Sciences Institute` (`ISI`). Using standard `MIME` types maximizes the interoperability of your application with other software now and in the future.
&emsp;&emsp;To support an additional media type, simply set the data in the `QMimeData` object with the `setData()` function, supplying the full `MIME` type and a `QByteArray` containing the data in the appropriate format. The following code takes a pixmap from a label and stores it as a `Portable Network Graphics` (`PNG`) file in a `QMimeData` object:

``` cpp
QByteArray output;
QBuffer outputBuffer ( &output );
outputBuffer.open ( QIODevice::WriteOnly );
imageLabel->pixmap()->toImage().save ( &outputBuffer, "PNG" );
mimeData->setData ( "image/png", output );
```

Of course, for this case we could have simply used `setImageData()` instead to supply image data in a variety of formats:

``` cpp
mimeData->setImageData ( QVariant ( *imageLabel->pixmap() ) );
```

The `QByteArray` approach is still useful in this case because it provides greater control over the amount of data stored in the `QMimeData` object. Note that custom datatypes used in item views must be declared as meta objects and that stream operators for them must be implemented.

### Drop Actions

&emsp;&emsp;In the clipboard model, the user can cut or copy the source information, then later paste it. Similarly in the drag and drop model, the user can drag a copy of the information or they can drag the information itself to a new place (moving it). The drag and drop model has an additional complication for the programmer: The program doesn't know whether the user wants to cut or copy the information until the operation is complete. This often makes no difference when dragging information between applications, but within an application it is important to check which drop action was used.
&emsp;&emsp;We can reimplement the `mouseMoveEvent()` for a widget, and start a drag and drop operation with a combination of possible drop actions. For example, we may want to ensure that dragging always moves objects in the widget:

``` cpp
void DragWidget::mouseMoveEvent ( QMouseEvent *event ) {
    if ( ! ( event->buttons() & Qt::LeftButton ) ) {
        return;
    }
​
    if ( ( event->pos() - dragStartPosition ).manhattanLength()
         < QApplication::startDragDistance() ) {
        return;
    }
​
    QDrag *drag = new QDrag ( this );
    QMimeData *mimeData = new QMimeData;
    mimeData->setData ( mimeType, data );
    drag->setMimeData ( mimeData );
    Qt::DropAction dropAction = drag->exec ( Qt::CopyAction | Qt::MoveAction );
    ...
}
```

&emsp;&emsp;The action returned by the `exec()` function may default to a `CopyAction` if the information is dropped into another application but, if it is dropped in another widget in the same application, we may obtain a different drop action.
&emsp;&emsp;The proposed drop actions can be filtered in a widget's `dragMoveEvent()` function. However, it is possible to accept all proposed actions in the `dragEnterEvent()` and let the user decide which they want to accept later:

``` cpp
void DragWidget::dragEnterEvent ( QDragEnterEvent *event ) {
    event->acceptProposedAction();
}
```

&emsp;&emsp;When a drop occurs in the widget, the `dropEvent()` handler function is called, and we can deal with each possible action in turn. First, we deal with drag and drop operations within the same widget:

``` cpp
void DragWidget::dropEvent ( QDropEvent *event ) {
    if ( event->source() == this && event->possibleActions() & Qt::MoveAction )
        return;
```

&emsp;&emsp;In this case, we refuse to deal with move operations. Each type of drop action that we accept is checked and dealt with accordingly:

``` cpp
    if ( event->proposedAction() == Qt::MoveAction ) {
        event->acceptProposedAction();
        /* Process the data from the event */
    } else if ( event->proposedAction() == Qt::CopyAction ) {
        event->acceptProposedAction();
        /* Process the data from the event */
    } else {
        /* Ignore the drop */
        return;
    }
    ...
}
```

Note that we checked for individual drop actions in the above code. As mentioned above in the section on `Overriding Proposed Actions`, it is sometimes necessary to override the proposed drop action and choose a different one from the selection of possible drop actions. To do this, you need to check for the presence of each action in the value supplied by the event's `possibleActions()`, set the drop action with `setDropAction()`, and call `accept()`.

### Drop Rectangles

&emsp;&emsp;The widget's `dragMoveEvent()` can be used to restrict drops to certain parts of the widget by only accepting the proposed drop actions when the cursor is within those areas. For example, the following code accepts any proposed drop actions when the cursor is over a child widget (`dropFrame`):

``` cpp
void Window::dragMoveEvent ( QDragMoveEvent *event ) {
    if ( event->mimeData()->hasFormat ( "text/plain" ) && \
         event->answerRect().intersects ( dropFrame->geometry() ) ) {
        event->acceptProposedAction();
    }
}
```

The `dragMoveEvent()` can also be used if you need to give visual feedback during a drag and drop operation, to scroll the window, or whatever is appropriate.

### The Clipboard

&emsp;&emsp;Applications can also communicate with each other by putting data on the clipboard. To access this, you need to obtain a `QClipboard` object from the `QApplication` object:

``` cpp
clipboard = QApplication::clipboard();
```

&emsp;&emsp;The `QMimeData` class is used to represent data that is transferred to and from the clipboard. To put data on the clipboard, you can use the `setText()`, `setImage()` and `setPixmap()` convenience functions for common data types. These functions are similar to those found in the `QMimeData` class, except that they also take an additional argument that controls where the data is stored: If `Clipboard` is specified, the data is placed on the clipboard; if `Selection` is specified, the data is placed in the mouse selection (on `X11` only). By default, data is put on the clipboard.
&emsp;&emsp;For example, we can copy the contents of a `QLineEdit` to the clipboard with the following code:

``` cpp
clipboard->setText ( lineEdit->text(), QClipboard::Clipboard );
```

&emsp;&emsp;Data with different `MIME` types can also be put on the clipboard. Construct a `QMimeData` object and set data with `setData()` function in the way described in the previous section; this object can then be put on the clipboard with the `setMimeData()` function.
&emsp;&emsp;The `QClipboard` class can notify the application about changes to the data it contains via its `dataChanged()` signal. For example, we can monitor the clipboard by connecting this signal to a slot in a widget:

``` cpp
connect ( clipboard, SIGNAL ( dataChanged() ), this, SLOT ( updateClipboard() ) );
```

&emsp;&emsp;The slot connected to this signal can read the data on the clipboard using one of the `MIME` types that can be used to represent it:

``` cpp
void ClipWindow::updateClipboard() {
    QStringList formats = clipboard->mimeData()->formats();
    QByteArray data = clipboard->mimeData()->data ( format );
    ...
}
```

&emsp;&emsp;The `selectionChanged()` signal can be used on `X11` to monitor the mouse selection.

### Interoperating with Other Applications

&emsp;&emsp;On `X11`, the public `XDND` protocol is used, while on `Windows` Qt uses the `OLE` standard, and Qt for `Mac OS X` uses the `Carbon Drag Manager`. On `X11`, `XDND` uses `MIME`, so no translation is necessary. The `Qt API` is the same regardless of the platform. On `Windows`, `MIME-aware` applications can communicate by using clipboard format names that are `MIME` types. Already some Windows applications use `MIME` naming conventions for their clipboard formats. Internally, `Qt` uses `QWindowsMime` and `QMacPasteboardMime` for translating proprietary clipboard formats to and from `MIME` types.
**Note**: The `Motif Drag & Drop Protocol` only allows receivers to request data in response to a `QDropEvent`. If you attempt to request data in response to e.g. a `QDragMoveEvent`, an empty `QByteArray` is returned.