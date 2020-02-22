---
title: Qt之QTableWidgetItem
categories: Qt语法详解
date: 2019-01-23 20:59:30
---
&emsp;&emsp;The `QTableWidgetItem` class provides an item for use with the `QTableWidget` class. The header file is `QTableWidgetItem`.<!--more-->

### Public Functions

Return                       | Function
-----------------------------|----------
                             | `QTableWidgetItem(int type = Type)`
                             | `QTableWidgetItem(const QString & text, int type = Type)`
                             | `QTableWidgetItem(const QIcon & icon, const QString & text, int type = Type)`
                             | `QTableWidgetItem(const QTableWidgetItem & other)`
`virtual`                    | `~QTableWidgetItem()`
`QBrush`                     | `background() const`
`Qt::CheckState`             | `checkState() const`
`virtual QTableWidgetItem *` | `clone() const`
`int`                        | `column() const`
`virtual QVariant`           | `data(int role) const`
`Qt::ItemFlags`              | `flags() const`
`QFont`                      | `font() const`
`QBrush`                     | `foreground() const`
`QIcon`                      | `icon() const`
`bool`                       | `isSelected() const`
`virtual void`               | `read(QDataStream & in)`
`int`                        | `row() const`
`void`                       | `setBackground(const QBrush & brush)`
`void`                       | `setCheckState(Qt::CheckState state)`
`virtual void`               | `setData(int role, const QVariant & value)`
`void`                       | `setFlags(Qt::ItemFlags flags)`
`void`                       | `setFont(const QFont & font)`
`void`                       | `setForeground(const QBrush & brush)`
`void`                       | `setIcon(const QIcon & icon)`
`void`                       | `setSelected(bool select)`
`void`                       | `setSizeHint(const QSize & size)`
`void`                       | `setStatusTip(const QString & statusTip)`
`void`                       | `setText(const QString & text)`
`void`                       | `setTextAlignment(int alignment)`
`void`                       | `setToolTip(const QString & toolTip)`
`void`                       | `setWhatsThis(const QString & whatsThis)`
`QSize`                      | `sizeHint() const`
`QString`                    | `statusTip() const`
`QTableWidget *`             | `tableWidget() const`
`QString`                    | `text() const`
`int`                        | `textAlignment() const`
`QString`                    | `toolTip() const`
`int`                        | `type() const`
`QString`                    | `whatsThis() const`
`virtual void`               | `write(QDataStream & out) const`
`virtual bool`               | `operator<(const QTableWidgetItem & other) const`
`QTableWidgetItem &`         | `operator=(const QTableWidgetItem & other)`

### Related Non-Members

- `QDataStream & operator<<(QDataStream & out, const QTableWidgetItem & item)`
- `QDataStream & operator>>(QDataStream & in, QTableWidgetItem & item)`

### Detailed Description

&emsp;&emsp;The `QTableWidgetItem` class provides an item for use with the `QTableWidget` class.
&emsp;&emsp;Table items are used to hold pieces of information for table widgets. Items usually contain `text`, `icons`, or `checkboxes`.
&emsp;&emsp;The `QTableWidgetItem` class is a convenience class that replaces the `QTableItem` class in `Qt 3`. It provides an item for use with the `QTableWidget` class.
&emsp;&emsp;`Top-level` items are constructed without a parent then inserted at the position specified by a pair of row and column numbers:

``` cpp
QTableWidgetItem *newItem = new QTableWidgetItem ( \
    tr ( "%1" ).arg ( pow ( row, column + 1 ) ) );
tableWidget->setItem ( row, column, newItem );
```

&emsp;&emsp;Each item can have its own background brush which is set with the `setBackground()` function. The current background brush can be found with `background()`. The text label for each item can be rendered with its own font and brush. These are specified with the `setFont()` and `setForeground()` functions, and read with `font()` and `foreground()`.
&emsp;&emsp;By default, items are enabled, editable, selectable, checkable, and can be used both as the source of a drag and drop operation and as a drop target. Each item's flags can be changed by calling `setFlags()` with the appropriate value. Checkable items can be checked and unchecked with the `setCheckState()` function. The corresponding `checkState()` function indicates whether the item is currently checked.

### Subclassing

&emsp;&emsp;When subclassing `QTableWidgetItem` to provide custom items, it is possible to define new types for them so that they can be distinguished from standard items. The constructors for subclasses that require this feature need to call the base class constructor with a new type value equal to or greater than `UserType`.

### Member Type Documentation

- enum `QTableWidgetItem::ItemType`: This enum describes the types that are used to describe table widget items.

Constant                     | Value  | Description
-----------------------------|--------|------------
`QTableWidgetItem::Type`     | `0`    | The default type for table widget items.
`QTableWidgetItem::UserType` | `1000` | The minimum value for custom types. Values below `UserType` are reserved by `Qt`.

You can define new user types in `QTableWidgetItem` subclasses to ensure that custom items are treated specially.

### Member Function Documentation

- `QTableWidgetItem::QTableWidgetItem(int type = Type)`: Constructs a table item of the specified `type` that does not belong to any table.
- `QTableWidgetItem::QTableWidgetItem(const QString & text, int type = Type)`: Constructs a table item with the given `text`.
- `QTableWidgetItem::QTableWidgetItem(const QIcon & icon, const QString & text, int type = Type)`: Constructs a table item with the given `icon` and `text`.
- `QTableWidgetItem::QTableWidgetItem(const QTableWidgetItem & other)`: Constructs a copy of `other`. Note that `type()` and `tableWidget()` are not copied. This function is useful when reimplementing `clone()`.
- `QTableWidgetItem::~QTableWidgetItem() [virtual]`: Destroys the table item.
- `QBrush QTableWidgetItem::background() const`: Returns the brush used to render the item's background.
- `Qt::CheckState QTableWidgetItem::checkState() const`: Returns the checked state of the table item.
- `QTableWidgetItem * QTableWidgetItem::clone() const [virtual]`: Creates a copy of the item.
- `int QTableWidgetItem::column() const`: Returns the column of the item in the table. If the item is not in a table, this function will return `-1`.
- `QVariant QTableWidgetItem::data(int role) const [virtual]` -- Returns the item's data for the given `role`.
- `Qt::ItemFlags QTableWidgetItem::flags() const`: Returns the flags used to describe the item. These determine whether the item can be checked, edited, and selected.
- `QFont QTableWidgetItem::font() const`: Returns the font used to render the item's text.
- `QBrush QTableWidgetItem::foreground() const`: Returns the brush used to render the item's foreground (e.g. text).
- `QIcon QTableWidgetItem::icon() const`: Returns the item's icon.
- `bool QTableWidgetItem::isSelected() const`: Returns `true` if the item is selected, otherwise returns `false`.
- `void QTableWidgetItem::read(QDataStream & in) [virtual]`: Reads the item from stream `in`.
- `int QTableWidgetItem::row() const`: Returns the row of the item in the table. If the item is not in a table, this function will return `-1`.
- `void QTableWidgetItem::setBackground(const QBrush & brush)`: Sets the item's background brush to the specified `brush`.
- `void QTableWidgetItem::setCheckState(Qt::CheckState state)`: Sets the check state of the table item to be `state`.
- `void QTableWidgetItem::setData(int role, const QVariant & value) [virtual]`: Sets the item's data for the given role to the specified `value`.
- `void QTableWidgetItem::setFlags(Qt::ItemFlags flags)`: Sets the flags for the item to the given `flags`. These determine whether the item can be selected or modified.
- `void QTableWidgetItem::setFont(const QFont & font)`: Sets the font used to display the item's text to the given `font`.
- `void QTableWidgetItem::setForeground(const QBrush & brush)`: Sets the item's foreground brush to the specified `brush`.
- `void QTableWidgetItem::setIcon(const QIcon & icon)`: Sets the item's icon to the `icon` specified.
- `void QTableWidgetItem::setSelected(bool select)`: Sets the selected state of the item to `select`.
- `void QTableWidgetItem::setSizeHint(const QSize & size)` -- Sets the size hint for the table item to be size. If no `size` hint is set, the item delegate will compute the size hint based on the item data.
- `void QTableWidgetItem::setStatusTip(const QString & statusTip)`: Sets the status tip for the table item to the text specified by `statusTip`. `QTableWidget` mouse tracking needs to be enabled for this feature to work.
- `void QTableWidgetItem::setText(const QString & text)`: Sets the item's text to the `text` specified.
- `void QTableWidgetItem::setTextAlignment(int alignment)`: Sets the text alignment for the item's text to the `alignment` specified.
- `void QTableWidgetItem::setToolTip(const QString & toolTip)`: Sets the item's tooltip to the string specified by `toolTip`.
- `void QTableWidgetItem::setWhatsThis(const QString & whatsThis)`: Sets the item's `What's This?` help to the string specified by `whatsThis`.
- `QSize QTableWidgetItem::sizeHint() const`: Returns the size hint set for the table item.
- `QString QTableWidgetItem::statusTip() const`: Returns the item's status tip.
- `QTableWidget * QTableWidgetItem::tableWidget() const`: Returns the table widget that contains the item.
- `QString QTableWidgetItem::text() const`: Returns the item's text.
- `int QTableWidgetItem::textAlignment() const`: Returns the text alignment for the item's text.
- `QString QTableWidgetItem::toolTip() const`: Returns the item's tooltip.
- `int QTableWidgetItem::type() const`: Returns the type passed to the `QTableWidgetItem` constructor.
- `QString QTableWidgetItem::whatsThis() const`: Returns the item's `What's This?` help.
- `void QTableWidgetItem::write(QDataStream & out) const [virtual]`: Writes the item to stream `out`.
- `bool QTableWidgetItem::operator<(const QTableWidgetItem & other) const [virtual]`: Returns `true` if the item is less than the `other` item; otherwise returns `false`.
- `QTableWidgetItem & QTableWidgetItem::operator=(const QTableWidgetItem & other)`: Assigns `other's` data and flags to this item. Note that `type()` and `tableWidget()` are not copied. This function is useful when reimplementing `clone()`.

### Related Non-Members

- `QDataStream & operator<<(QDataStream & out, const QTableWidgetItem & item)`: Writes the table widget item `item` to stream `out`. This operator uses `QTableWidgetItem::write()`.
- `QDataStream & operator>>(QDataStream & in, QTableWidgetItem & item)`: Reads a table widget item from stream `in` into `item`. This operator uses `QTableWidgetItem::read()`.