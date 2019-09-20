---
title: Qt之QDrag
date: 2019-01-22 18:17:51
categories: Qt语法详解
---
&emsp;&emsp;The `QDrag` class provides support for `MIME-based` drag and drop data transfer.

Header  | Inherits
--------|---------
`QDrag` | `QObject`

### Public Functions

Return           | Function
-----------------|---------
                 | `QDrag(QWidget * dragSource)`
                 | `~QDrag()`
`Qt::DropAction` | `exec(Qt::DropActions supportedActions = Qt::MoveAction)`
`Qt::DropAction` | `exec(Qt::DropActions supportedActions, Qt::DropAction defaultDropAction)`
`QPoint`         | `hotSpot() const`
`QMimeData *`    | `mimeData() const`
`QPixmap`        | `pixmap() const`
`void`           | `setDragCursor(const QPixmap & cursor, Qt::DropAction action)`
`void`           | `setHotSpot(const QPoint & hotspot)`
`void`           | `setMimeData(QMimeData * data)`
`void`           | `setPixmap(const QPixmap & pixmap)`
`QWidget *`      | `source() const`
`QWidget *`      | `target() const`

### Signals

- `void actionChanged(Qt::DropAction action)`
- `void targetChanged(QWidget * newTarget)`

### Detailed Description

&emsp;&emsp;The `QDrag` class provides support for `MIME-based` drag and drop data transfer.
&emsp;&emsp;Drag and drop is an intuitive way for users to copy or move data around in an application, and is used in many desktop environments as a mechanism for copying data between applications. Drag and drop support in `Qt` is centered around the `QDrag` class that handles most of the details of a drag and drop operation.
&emsp;&emsp;The data to be transferred by the drag and drop operation is contained in a `QMimeData` object. This is specified with the `setMimeData()` function in the following way:

``` cpp
QDrag *drag = new QDrag ( this );
QMimeData *mimeData = new QMimeData;
​
mimeData->setText ( commentEdit->toPlainText() );
drag->setMimeData ( mimeData );
```

&emsp;&emsp;Note that `setMimeData()` assigns ownership of the `QMimeData` object to the `QDrag` object. The `QDrag` must be constructed on the heap with a parent `QWidget` to ensure that `Qt` can clean up after the drag and drop operation has been completed.
&emsp;&emsp;A pixmap can be used to represent the data while the drag is in progress, and will move with the cursor to the drop target. This pixmap typically shows an icon that represents the `MIME` type of the data being transferred, but any pixmap can be set with `setPixmap()`. The cursor's hot spot can be given a position relative to the `top-left` corner of the pixmap with the `setHotSpot()` function. The following code positions the pixmap so that the cursor's hot spot points to the center of its bottom edge:

``` cpp
drag->setHotSpot ( QPoint ( drag->pixmap().width() / 2, drag->pixmap().height() ) );
```

&emsp;&emsp;**Note**: On `X11`, the pixmap may not be able to keep up with the mouse movements if the hot spot causes the pixmap to be displayed directly under the cursor.
&emsp;&emsp;The source and target widgets can be found with `source()` and `target()`. These functions are often used to determine whether drag and drop operations started and finished at the same widget, so that special behavior can be implemented.
&emsp;&emsp;`QDrag` only deals with the drag and drop operation itself. It is up to the developer to decide when a drag operation begins, and how a `QDrag` object should be constructed and used. For a given widget, it is often necessary to reimplement `mousePressEvent()` to determine whether the user has pressed a mouse button, and reimplement `mouseMoveEvent()` to check whether a `QDrag` is required.

### Member Function Documentation

- `QDrag::QDrag(QWidget * dragSource)`: Constructs a new drag object for the widget specified by `dragSource`.
- `QDrag::~QDrag()`: Destroys the drag object.
- `void QDrag::actionChanged(Qt::DropAction action) [signal]`: This `signal` is emitted when the `action` associated with the drag changes.
- `Qt::DropAction QDrag::exec(Qt::DropActions supportedActions = Qt::MoveAction)`: Starts the drag and drop operation and returns a value indicating the requested drop action when it is completed. The drop actions that the user can choose from are specified in `supportedActions`. The default proposed action will be selected among the allowed actions in the following order: `Move`, `Copy` and `Link`. **Note**: On `Linux` and `Mac OS X`, the drag and drop operation can take some time, but this function does not block the event loop. Other events are still delivered to the application while the operation is performed. On `Windows`, the `Qt` event loop is blocked while during the operation.
- `Qt::DropAction QDrag::exec(Qt::DropActions supportedActions, Qt::DropAction defaultDropAction)`: Starts the drag and drop operation and returns a value indicating the requested drop action when it is completed. The drop actions that the user can choose from are specified in `supportedActions`. The `defaultDropAction` determines which action will be proposed when the user performs a drag without using modifier keys. **Note**: On `Linux` and `Mac OS X`, the drag and drop operation can take some time, but this function does not block the event loop. Other events are still delivered to the application while the operation is performed. On `Windows`, the `Qt` event loop is blocked during the operation. However, `QDrag::exec()` on `Windows` causes `processEvents()` to be called frequently to keep the `GUI` responsive. If any loops or operations are called while a drag operation is active, it will block the drag operation.
- `QPoint QDrag::hotSpot() const`: Returns the position of the hot spot relative to the `top-left` corner of the cursor.
- `QMimeData * QDrag::mimeData() const`: Returns the `MIME` data that is encapsulated by the drag object.
- `QPixmap QDrag::pixmap() const`: Returns the pixmap used to represent the data in a drag and drop operation.
- `void QDrag::setDragCursor(const QPixmap & cursor, Qt::DropAction action)`: Sets the drag `cursor` for the `action`. This allows you to override the default native cursors. To revert to using the native `cursor` for `action` pass in a null `QPixmap` as `cursor`. The `action` can only be `CopyAction`, `MoveAction` or `LinkAction`. All other values of `DropAction` are ignored.
- `void QDrag::setHotSpot(const QPoint & hotspot)`: Sets the position of the hot spot relative to the `top-left` corner of the pixmap used to the point specified by `hotspot`. **Note**: on `X11`, the pixmap may not be able to keep up with the mouse movements if the hot spot causes the pixmap to be displayed directly under the cursor.
- `void QDrag::setMimeData(QMimeData * data)`: Sets the data to be sent to the given `MIME` `data`. Ownership of the data is transferred to the `QDrag` object.
- `void QDrag::setPixmap(const QPixmap & pixmap)`: Sets pixmap as the `pixmap` used to represent the data in a drag and drop operation. You can only set a pixmap before the drag is started.
- `QWidget * QDrag::source() const`: Returns the source of the drag object. This is the widget where the drag and drop operation originated.
- `QWidget * QDrag::target() const`: Returns the target of the drag and drop operation. This is the widget where the drag object was dropped.
- `void QDrag::targetChanged(QWidget * newTarget) [signal]`: This `signal` is emitted when the target of the drag and drop operation changes, with `newTarget` the new target.