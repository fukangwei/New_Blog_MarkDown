---
title: Qt之QMimeData
date: 2019-01-25 15:09:10
categories: Qt语法详解
---
&emsp;&emsp;The `QMimeData` class provides a container for data that records information about its `MIME` type.

Header      | Inherits
------------|---------
`QMimeData` | `QObject`

### Public Functions

Return                | Function
----------------------|---------
                      | `QMimeData()`
                      | `~QMimeData()`
`void`                | `clear()`
`QVariant`            | `colorData() const`
`QByteArray`          | `data(const QString & mimeType) const`
`virtual QStringList` | `formats() const`
`bool`                | `hasColor() const`
`virtual bool`        | `hasFormat(const QString & mimeType) const`
`bool`                | `hasHtml() const`
`bool`                | `hasImage() const`
`bool`                | `hasText() const`
`bool`                | `hasUrls() const`
`QString`             | `html() const`
`QVariant`            | `imageData() const`
`void`                | `removeFormat(const QString & mimeType)`
`void`                | `setColorData(const QVariant & color)`
`void`                | `setData(const QString & mimeType, const QByteArray & data)`
`void`                | `setHtml(const QString & html)`
`void`                | `setImageData(const QVariant & image)`
`void`                | `setText(const QString & text)`
`void`                | `setUrls(const QList<QUrl> & urls)`
`QString`             | `text() const`
`QList<QUrl>`         | `urls() const`

### Protected Functions

Return             | Function
-------------------|---------
`virtual QVariant` | `retrieveData(const QString & mimeType, QVariant::Type type) const`

### Detailed Description

&emsp;&emsp;The `QMimeData` class provides a container for data that records information about its `MIME` type.
&emsp;&emsp;`QMimeData` is used to describe information that can be stored in the clipboard, and transferred via the drag and drop mechanism. `QMimeData` objects associate the data that they hold with the corresponding `MIME` types to ensure that information can be safely transferred between applications, and copied around within the same application.
&emsp;&emsp;`QMimeData` objects are usually created using new and supplied to `QDrag` or `QClipboard` objects. This is to enable `Qt` to manage the memory that they use.
&emsp;&emsp;A single `QMimeData` object can store the same data using several different formats at the same time. The `formats()` function returns a list of the available formats in order of preference. The `data()` function returns the raw data associated with a `MIME` type, and `setData()` allows you to set the data for a `MIME` type.
&emsp;&emsp;For the most common `MIME` types, `QMimeData` provides convenience functions to access the data:

Tester       | Getter        | Setter           | MIME Types
-------------|---------------|------------------|-----------
`hasText()`  | `text()`      | `setText()`      | `text/plain`
`hasHtml()`  | `html()`      | `setHtml()`      | `text/html`
`hasUrls()`  | `urls()`      | `setUrls()`      | `text/uri-list`
`hasImage()` | `imageData()` | `setImageData()` | `image/ *`
`hasColor()` | `colorData()` | `setColorData()` | `application/x-color`

&emsp;&emsp;For example, if your write a widget that accepts `URL` drags, you would end up writing code like this:

``` cpp
void MyWidget::dragEnterEvent ( QDragEnterEvent *event ) {
    if ( event->mimeData()->hasUrls() ) {
        event->acceptProposedAction();
    }
}
​
void MyWidget::dropEvent ( QDropEvent *event ) {
    if ( event->mimeData()->hasUrls() ) {
        foreach ( QUrl url, event->mimeData()->urls() ) {
            ...
        }
    }
}
```

&emsp;&emsp;There are three approaches for storing custom data in a `QMimeData` object:
&emsp;&emsp;1. Custom data can be stored directly in a `QMimeData` object as a `QByteArray` using `setData()`. For example:

``` cpp
QByteArray csvData = ...;
​
QMimeData *mimeData = new QMimeData;
mimeData->setData ( "text/csv", csvData );
```

&emsp;&emsp;2. We can subclass `QMimeData` and reimplement `hasFormat()`, `formats()`, and `retrieveData()`.
&emsp;&emsp;3. If the drag and drop operation occurs within a single application, we can subclass `QMimeData` and add extra data in it, and use a `qobject_cast()` in the receiver's drop event handler. For example:

``` cpp
void MyWidget::dropEvent ( QDropEvent *event ) {
    const MyMimeData *myData = qobject_cast<const MyMimeData *> ( event->mimeData() );
​
    if ( myData ) {
        /* access myData's data directly (not through QMimeData's API) */
    }
}
```

### Platform-Specific MIME Types

&emsp;&emsp;On `Windows`, `formats()` will also return custom formats available in the `MIME` data, using the `x-qt-windows-mime` subtype to indicate that they represent data in `non-standard` formats. The formats will take the following form:

``` xml
application/x-qt-windows-mime; value="<custom type>"
```

&emsp;&emsp;The following are examples of custom `MIME` types:

``` xml
application/x-qt-windows-mime; value="FileGroupDescriptor"
application/x-qt-windows-mime; value="FileContents"
```

&emsp;&emsp;The value declaration of each format describes the way in which the data is encoded.
&emsp;&emsp;On `Windows`, the `MIME` format does not always map directly to the clipboard formats. `Qt` provides `QWindowsMime` to map clipboard formats to `open-standard` `MIME` formats. Similarly, the `QMacPasteboardMime` maps `MIME` to `Mac` flavors.

### Member Function Documentation

- `QMimeData::QMimeData()`: Constructs a new `MIME` data object with no data in it.
- `QMimeData::~QMimeData()`: Destroys the `MIME` data object.
- `void QMimeData::clear()`: Removes all the `MIME` type and data entries in the object.
- `QVariant QMimeData::colorData() const`: Returns a color if the data stored in the object represents a color (`MIME` type `application/x-color`); otherwise returns a null variant. A `QVariant` is used because `QMimeData` belongs to the `QtCore` library, whereas `QColor` belongs to `QtGui`. To convert the `QVariant` to a `QColor`, simply use `qvariant_cast()`. For example:

``` cpp
if ( event->mimeData()->hasColor() ) {
    QColor color = qvariant_cast<QColor> ( event->mimeData()->colorData() );
    ...
}
```

- `QByteArray QMimeData::data(const QString & mimeType) const`: Returns the data stored in the object in the format described by the `MIME` type specified by `mimeType`.
- `[virtual] QStringList QMimeData::formats() const`: Returns a list of formats supported by the object. This is a list of `MIME` types for which the object can return suitable data. The formats in the list are in a priority order. For the most common types of data, you can call the `higher-level` functions `hasText()`, `hasHtml()`, `hasUrls()`, `hasImage()`, and `hasColor()` instead.
- `bool QMimeData::hasColor() const`: Returns `true` if the object can return a color (`MIME` type `application/x-color`); otherwise returns `false`.
- `[virtual] bool QMimeData::hasFormat(const QString & mimeType) const`: Returns `true` if the object can return data for the `MIME` type specified by `mimeType`; otherwise returns `false`. For the most common types of data, you can call the `higher-level` functions `hasText()`, `hasHtml()`, `hasUrls()`, `hasImage()`, and `hasColor()` instead.
- `bool QMimeData::hasHtml() const`: Returns `true` if the object can return `HTML` (`MIME` type `text/html`); otherwise returns `false`.
- `bool QMimeData::hasImage() const`: Returns `true` if the object can return an image; otherwise returns `false`.
- `bool QMimeData::hasText() const`: Returns `true` if the object can return plain text (`MIME` type `text/plain`); otherwise returns `false`.
- `bool QMimeData::hasUrls() const`: Returns `true` if the object can return a list of urls; otherwise returns `false`. `URLs` correspond to the `MIME` type `text/uri-list`.
- `QString QMimeData::html() const`: Returns a string if the data stored in the object is `HTML` (`MIME` type `text/html`); otherwise returns an empty string.
- `QVariant QMimeData::imageData() const`: Returns a `QVariant` storing a `QImage` if the object can return an image; otherwise returns a null variant. A `QVariant` is used because `QMimeData` belongs to the `QtCore` library, whereas `QImage` belongs to `QtGui`. To convert the `QVariant` to a `QImage`, simply use `qvariant_cast()`. For example:

``` cpp
if ( event->mimeData()->hasImage() ) {
    QImage image = qvariant_cast<QImage> ( event->mimeData()->imageData() );
    ...
}
```

- `void QMimeData::removeFormat(const QString & mimeType)`: Removes the data entry for `mimeType` in the object.
- `[virtual protected] QVariant QMimeData::retrieveData(const QString & mimeType, QVariant::Type type) const`: Returns a variant with the given `type` containing data for the `MIME` type specified by `mimeType`. If the object does not support the `MIME` type or variant `type` given, a null variant is returned instead. This function is called by the general `data()` getter and by the convenience getters (`text()`, `html()`, `urls()`, `imageData()`, and `colorData()`). You can reimplement it if you want to store your data using a custom data structure (instead of a `QByteArray`, which is what `setData()` provides). You would then also need to reimplement `hasFormat()` and `formats()`.
- `void QMimeData::setColorData(const QVariant & color)`: Sets the color data in the object to the given `color`. Colors correspond to the `MIME` type `application/x-color`.
- `void QMimeData::setData(const QString & mimeType, const QByteArray & data)`: Sets the data associated with the `MIME` type given by `mimeType` to the specified `data`. For the most common types of `data`, you can call the `higher-level` functions `setText()`, `setHtml()`, `setUrls()`, `setImageData()`, and `setColorData()` instead. Note that if you want to use a custom `data` type in an item view drag and drop operation, you must register it as a `Qt` meta type, using the `Q_DECLARE_METATYPE()` macro, and implement stream operators for it. The stream operators must then be registered with the `qRegisterMetaTypeStreamOperators()` function.
- `void QMimeData::setHtml(const QString & html)`: Sets `html` as the `HTML` (`MIME` type `text/html`) used to represent the data.
- `void QMimeData::setImageData(const QVariant & image)`: Sets the data in the object to the given `image`. A `QVariant` is used because `QMimeData` belongs to the `QtCore` library, whereas `QImage` belongs to `QtGui`. The conversion from `QImage` to `QVariant` is implicit. For example:

``` cpp
mimeData->setImageData ( QImage ( "beautifulfjord.png" ) );
```

- `void QMimeData::setText(const QString & text)`: Sets text as the plain `text` (`MIME` type `text/plain`) used to represent the data.
- `void QMimeData::setUrls(const QList<QUrl> & urls)`: Sets the `URLs` stored in the `MIME` data object to those specified by `urls`. `URLs` correspond to the `MIME` type `text/uri-list`.
- `QString QMimeData::text() const`: Returns a plain text (`MIME` type `text/plain`) representation of the data.
- `QList<QUrl> QMimeData::urls() const`: Returns a list of `URLs` contained within the `MIME` data object. `URLs` correspond to the `MIME` type `text/uri-list`.