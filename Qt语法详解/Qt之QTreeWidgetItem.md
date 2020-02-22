---
title: Qt之QTreeWidgetItem
categories: Qt语法详解
date: 2019-01-27 00:36:51
---
&emsp;&emsp;The `QTreeWidgetItem` class provides an item for use with the `QTreeWidget` convenience class. The header file is `QTreeWidgetItem`.<!--more-->

### Public Functions

Return                                  | Function
----------------------------------------|---------
                                        | `QTreeWidgetItem(int type = Type)`
                                        | `QTreeWidgetItem(const QStringList & strings, int type = Type)`
                                        | `QTreeWidgetItem(QTreeWidget * parent, int type = Type)`
                                        | `QTreeWidgetItem(QTreeWidget * parent, const QStringList & strings, int type = Type)`
                                        | `QTreeWidgetItem(QTreeWidget * parent, QTreeWidgetItem * preceding, int type = Type)`
                                        | `QTreeWidgetItem(QTreeWidgetItem * parent, int type = Type)`
                                        | `QTreeWidgetItem(QTreeWidgetItem * parent, const QStringList & strings, int type = Type)`
                                        | `QTreeWidgetItem(QTreeWidgetItem * parent, QTreeWidgetItem * preceding, int type = Type)`
                                        | `QTreeWidgetItem(const QTreeWidgetItem & other)`
`virtual`                               | `~QTreeWidgetItem()`
`void`                                  | `addChild(QTreeWidgetItem * child)`
`void`                                  | `addChildren(const QList<QTreeWidgetItem *> & children)`
`QBrush`                                | `background(int column) const`
`Qt::CheckState`                        | `checkState(int column) const`
`QTreeWidgetItem *`                     | `child(int index) const`
`int`                                   | `childCount() const`
`QTreeWidgetItem::ChildIndicatorPolicy` | `childIndicatorPolicy() const`
`virtual QTreeWidgetItem *`             | `clone() const`
`int`                                   | `columnCount() const`
`virtual QVariant`                      | `data(int column, int role) const`
`Qt::ItemFlags`                         | `flags() const`
`QFont`                                 | `font(int column) const`
`QBrush`                                | `foreground(int column) const`
`QIcon`                                 | `icon(int column) const`
`int`                                   | `indexOfChild(QTreeWidgetItem * child) const`
`void`                                  | `insertChild(int index, QTreeWidgetItem * child)`
`void`                                  | `insertChildren(int index, const QList<QTreeWidgetItem *> & children)`
`bool`                                  | `isDisabled() const`
`bool`                                  | `isExpanded() const`
`bool`                                  | `isFirstColumnSpanned() const`
`bool`                                  | `isHidden() const`
`bool`                                  | `isSelected() const`
`QTreeWidgetItem *`                     | `parent() const`
`virtual void`                          | `read(QDataStream & in)`
`void`                                  | `removeChild(QTreeWidgetItem * child)`
`void`                                  | `setBackground(int column, const QBrush & brush)`
`void`                                  | `setCheckState(int column, Qt::CheckState state)`
`void`                                  | `setChildIndicatorPolicy(QTreeWidgetItem::ChildIndicatorPolicy policy)`
`virtual void`                          | `setData(int column, int role, const QVariant & value)`
`void`                                  | `setDisabled(bool disabled)`
`void`                                  | `setExpanded(bool expand)`
`void`                                  | `setFirstColumnSpanned(bool span)`
`void`                                  | `setFlags(Qt::ItemFlags flags)`
`void`                                  | `setFont(int column, const QFont & font)`
`void`                                  | `setForeground(int column, const QBrush & brush)`
`void`                                  | `setHidden(bool hide)`
`void`                                  | `setIcon(int column, const QIcon & icon)`
`void`                                  | `setSelected(bool select)`
`void`                                  | `setSizeHint(int column, const QSize & size)`
`void`                                  | `setStatusTip(int column, const QString & statusTip)`
`void`                                  | `setText(int column, const QString & text)`
`void`                                  | `setTextAlignment(int column, int alignment)`
`void`                                  | `setToolTip(int column, const QString & toolTip)`
`void`                                  | `setWhatsThis(int column, const QString & whatsThis)`
`QSize`                                 | `sizeHint(int column) const`
`void`                                  | `sortChildren(int column, Qt::SortOrder order)`
`QString`                               | `statusTip(int column) const`
`QTreeWidgetItem *`                     | `takeChild(int index)`
`QList<QTreeWidgetItem *>`              | `takeChildren()`
`QString`                               | `text(int column) const`
`int`                                   | `textAlignment(int column) const`
`QString`                               | `toolTip(int column) const`
`QTreeWidget *`                         | `treeWidget() const`
`int`                                   | `type() const`
`QString`                               | `whatsThis(int column) const`
`virtual void`                          | `write(QDataStream & out) const`
`virtual bool`                          | `operator<(const QTreeWidgetItem & other) const`
`QTreeWidgetItem &`                     | `operator=(const QTreeWidgetItem & other)`

### Protected Functions

- `void emitDataChanged()`

### Related Non-Members

- `QDataStream & operator<<(QDataStream & out, const QTreeWidgetItem & item)`
- `QDataStream & operator>>(QDataStream & in, QTreeWidgetItem & item)`

### Detailed Description

&emsp;&emsp;The `QTreeWidgetItem` class provides an item for use with the `QTreeWidget` convenience class.
&emsp;&emsp;Tree widget items are used to hold rows of information for tree widgets. Rows usually contain several columns of data, each of which can contain a text label and an icon.
&emsp;&emsp;The `QTreeWidgetItem` class is a convenience class that replaces the `QListViewItem` class in `Qt 3`. It provides an item for use with the `QTreeWidget` class.
&emsp;&emsp;Items are usually constructed with a parent that is either a `QTreeWidget` (for `top-level` items) or a `QTreeWidgetItem` (for items on lower levels of the tree). For example, the following code constructs a `top-level` item to represent cities of the world, and adds a entry for `Oslo` as a child item:

``` cpp
QTreeWidgetItem *cities = new QTreeWidgetItem ( treeWidget );
cities->setText ( 0, tr ( "Cities" ) );
QTreeWidgetItem *osloItem = new QTreeWidgetItem ( cities );
osloItem->setText ( 0, tr ( "Oslo" ) );
osloItem->setText ( 1, tr ( "Yes" ) );
```

Items can be added in a particular order by specifying the item they follow when they are constructed:

``` cpp
QTreeWidgetItem *planets = new QTreeWidgetItem ( treeWidget, cities );
planets->setText ( 0, tr ( "Planets" ) );
```

&emsp;&emsp;Each column in an item can have its own background brush which is set with the `setBackground()` function. The current background brush can be found with `background()`. The text label for each column can be rendered with its own font and brush. These are specified with the `setFont()` and `setForeground()` functions, and read with `font()` and `foreground()`.
&emsp;&emsp;The main difference between `top-level` items and those in lower levels of the tree is that a `top-level` item has no `parent()`. This information can be used to tell the difference between items, and is useful to know when inserting and removing items from the tree. Children of an item can be removed with `takeChild()` and inserted at a given index in the list of children with the `insertChild()` function.
&emsp;&emsp;By default, items are enabled, selectable, checkable, and can be the source of a drag and drop operation. Each item's flags can be changed by calling `setFlags()` with the appropriate value. Checkable items can be checked and unchecked with the `setCheckState()` function. The corresponding `checkState()` function indicates whether the item is currently checked.

### Subclassing

&emsp;&emsp;When subclassing `QTreeWidgetItem` to provide custom items, it is possible to define new types for them so that they can be distinguished from standard items. The constructors for subclasses that require this feature need to call the base class constructor with a new type value equal to or greater than `UserType`.

### Member Type Documentation

- enum `QTreeWidgetItem::ChildIndicatorPolicy`:

Constant                                          | Value | Description
--------------------------------------------------|-------|-------------------------------------------------------
`QTreeWidgetItem::ShowIndicator`                  | `0`   | The controls for expanding and collapsing will be shown for this item even if there are no children.
`QTreeWidgetItem::DontShowIndicator`              | `1`   | The controls for expanding and collapsing will never be shown even if there are children. If the node is forced open the user will not be able to expand or collapse the item.
`QTreeWidgetItem::DontShowIndicatorWhenChildless` | `2`   | The controls for expanding and collapsing will be shown if the item contains children.

- enum `QTreeWidgetItem::ItemType`: This enum describes the types that are used to describe tree widget items.

Constant                    | Value  | Description
----------------------------|--------|------------
`QTreeWidgetItem::Type`     | `0`    | The default type for tree widget items.
`QTreeWidgetItem::UserType` | `1000` | The minimum value for custom types. Values below `UserType` are reserved by `Qt`.

You can define new user types in `QTreeWidgetItem` subclasses to ensure that custom items are treated specially; for example, when items are sorted.

### Member Function Documentation

- `QTreeWidgetItem::QTreeWidgetItem(int type = Type)`: Constructs a tree widget item of the specified `type`. The item must be inserted into a tree widget.
- `QTreeWidgetItem::QTreeWidgetItem(const QStringList & strings, int type = Type)`: Constructs a tree widget item of the specified `type`. The item must be inserted into a tree widget. The given list of `strings` will be set as the item text for each column in the item.
- `QTreeWidgetItem::QTreeWidgetItem(QTreeWidget * parent, int type = Type)`: Constructs a tree widget item of the specified `type` and appends it to the items in the given `parent`.
- `QTreeWidgetItem::QTreeWidgetItem(QTreeWidget * parent, const QStringList & strings, int type = Type)`: Constructs a tree widget item of the specified `type` and appends it to the items in the given `parent`. The given list of `strings` will be set as the item text for each column in the item.
- `QTreeWidgetItem::QTreeWidgetItem(QTreeWidget * parent, QTreeWidgetItem * preceding, int type = Type)`: Constructs a tree widget item of the specified `type` and inserts it into the given `parent` after the `preceding` item.
- `QTreeWidgetItem::QTreeWidgetItem(QTreeWidgetItem * parent, int type = Type)`: Constructs a tree widget item and append it to the given `parent`.
- `QTreeWidgetItem::QTreeWidgetItem(QTreeWidgetItem * parent, const QStringList & strings, int type = Type)`: Constructs a tree widget item and append it to the given `parent`. The given list of `strings` will be set as the item text for each column in the item.
- `QTreeWidgetItem::QTreeWidgetItem(QTreeWidgetItem * parent, QTreeWidgetItem * preceding, int type = Type)`: Constructs a tree widget item of the specified `type` that is inserted into the `parent` after the `preceding` child item.
- `QTreeWidgetItem::QTreeWidgetItem(const QTreeWidgetItem & other)`: Constructs a copy of `other`. Note that `type()` and `treeWidget()` are not copied. This function is useful when reimplementing `clone()`.
- `QTreeWidgetItem::~QTreeWidgetItem() [virtual]`: Destroys this tree widget item. The item will be removed from `QTreeWidgets` to which it has been added. This makes it safe to delete an item at any time.
- `void QTreeWidgetItem::addChild(QTreeWidgetItem * child)`: Appends the `child` item to the list of children.
- `void QTreeWidgetItem::addChildren(const QList<QTreeWidgetItem *> & children)`: Appends the given list of `children` to the item.
- `QBrush QTreeWidgetItem::background(int column) const`: Returns the brush used to render the background of the specified `column`.
- `Qt::CheckState QTreeWidgetItem::checkState(int column) const`: Returns the check state of the label in the given `column`.
- `QTreeWidgetItem * QTreeWidgetItem::child(int index) const`: Returns the item at the given `index` in the list of the item's children.
- `int QTreeWidgetItem::childCount() const`: Returns the number of child items.
- `QTreeWidgetItem::ChildIndicatorPolicy QTreeWidgetItem::childIndicatorPolicy() const`: Returns the item indicator policy. This policy decides when the tree branch `expand/collapse` indicator is shown.
- `QTreeWidgetItem * QTreeWidgetItem::clone() const [virtual]`: Creates a deep copy of the item and of its children.
- `int QTreeWidgetItem::columnCount() const`: Returns the number of columns in the item.
- `QVariant QTreeWidgetItem::data(int column, int role) const [virtual]`: Returns the value for the item's `column` and `role`.
- `void QTreeWidgetItem::emitDataChanged() [protected]`: Causes the model associated with this item to emit a `dataChanged()` signal for this item. You normally only need to call this function if you have subclassed `QTreeWidgetItem` and reimplemented `data()` and/or `setData()`.
- `Qt::ItemFlags QTreeWidgetItem::flags() const`: Returns the flags used to describe the item. These determine whether the item can be checked, edited, and selected. The default value for flags is `Qt::ItemIsSelectable | Qt::ItemIsUserCheckable | Qt::ItemIsEnabled | Qt::ItemIsDragEnabled`. If the item was constructed with a parent, flags will in addition contain `Qt::ItemIsDropEnabled`.
- `QFont QTreeWidgetItem::font(int column) const`: Returns the font used to render the text in the specified `column`.
- `QBrush QTreeWidgetItem::foreground(int column) const`: Returns the brush used to render the foreground (e.g. `text`) of the specified `column`.
- `QIcon QTreeWidgetItem::icon(int column) const`: Returns the icon that is displayed in the specified `column`.
- `int QTreeWidgetItem::indexOfChild(QTreeWidgetItem * child) const`: Returns the index of the given `child` in the item's list of children.
- `void QTreeWidgetItem::insertChild(int index, QTreeWidgetItem * child)`: Inserts the `child` item at `index` in the list of children. If the `child` has already been inserted somewhere else it wont be inserted again.
- `void QTreeWidgetItem::insertChildren(int index, const QList<QTreeWidgetItem *> & children)`: Inserts the given list of `children` into the list of the item `children` at `index`. Children that have already been inserted somewhere else wont be inserted.
- `bool QTreeWidgetItem::isDisabled() const`: Returns `true` if the item is disabled; otherwise returns `false`.
- `bool QTreeWidgetItem::isExpanded() const`: Returns `true` if the item is expanded, otherwise returns `false`.
- `bool QTreeWidgetItem::isFirstColumnSpanned() const`: Returns `true` if the item is spanning all the columns in a row; otherwise returns `false`.
- `bool QTreeWidgetItem::isHidden() const`: Returns `true` if the item is hidden, otherwise returns `false`.
- `bool QTreeWidgetItem::isSelected() const`: Returns `true` if the item is selected, otherwise returns `false`.
- `QTreeWidgetItem * QTreeWidgetItem::parent() const`: Returns the item's parent.
- `void QTreeWidgetItem::read(QDataStream & in) [virtual]`: Reads the item from stream `in`. This only reads data into a single item.
- `void QTreeWidgetItem::removeChild(QTreeWidgetItem * child)`: Removes the given item indicated by `child`. The removed item will not be deleted.
- `void QTreeWidgetItem::setBackground(int column, const QBrush & brush)`: Sets the background `brush` of the label in the given `column` to the specified `brush`.
- `void QTreeWidgetItem::setCheckState(int column, Qt::CheckState state)`: Sets the item in the given `column` check state to be `state`.
- `void QTreeWidgetItem::setChildIndicatorPolicy(QTreeWidgetItem::ChildIndicatorPolicy policy)`: Sets the item indicator `policy`. This `policy` decides when the tree branch `expand/collapse` indicator is shown. The default value is `ShowForChildren`.
- `void QTreeWidgetItem::setData(int column, int role, const QVariant & value) [virtual]`: Sets the `value` for the item's `column` and `role` to the given `value`. The `role` describes the type of data specified by `value`, and is defined by the `Qt::ItemDataRole` enum.
- `void QTreeWidgetItem::setDisabled(bool disabled)`: Disables the item if `disabled` is `true`; otherwise enables the item.
- `void QTreeWidgetItem::setExpanded(bool expand)`: Expands the item if `expand` is true, otherwise collapses the item. **Warning**: The `QTreeWidgetItem` must be added to the `QTreeWidget` before calling this function.
- `void QTreeWidgetItem::setFirstColumnSpanned(bool span)`: Sets the first section to `span` all columns if `span` is `true`; otherwise all item sections are shown.
- `void QTreeWidgetItem::setFlags(Qt::ItemFlags flags)`: Sets the `flags` for the item to the given `flags`. These determine whether the item can be selected or modified. This is often used to disable an item.
- `void QTreeWidgetItem::setFont(int column, const QFont & font)`: Sets the `font` used to display the text in the given `column` to the given `font`.
- `void QTreeWidgetItem::setForeground(int column, const QBrush & brush)`: Sets the foreground `brush` of the label in the given `column` to the specified `brush`.
- `void QTreeWidgetItem::setHidden(bool hide)`: Hides the item if `hide` is `true`, otherwise shows the item.
- `void QTreeWidgetItem::setIcon(int column, const QIcon & icon)`: Sets the icon to be displayed in the given `column` to `icon`.
- `void QTreeWidgetItem::setSelected(bool select)`: Sets the selected state of the item to `select`.
- `void QTreeWidgetItem::setSizeHint(int column, const QSize & size)`: Sets the size hint for the tree item in the given `column` to be `size`. If no `size` hint is set, the item delegate will compute the `size` hint based on the item data.
- `void QTreeWidgetItem::setStatusTip(int column, const QString & statusTip)`: Sets the status tip for the given `column` to the given `statusTip`. `QTreeWidget` mouse tracking needs to be enabled for this feature to work.
- `void QTreeWidgetItem::setText(int column, const QString & text)`: Sets the text to be displayed in the given `column` to the given `text`.
- `void QTreeWidgetItem::setTextAlignment(int column, int alignment)`: Sets the text alignment for the label in the given `column` to the `alignment` specified.
- `void QTreeWidgetItem::setToolTip(int column, const QString & toolTip)`: Sets the tooltip for the given `column` to `toolTip`.
- `void QTreeWidgetItem::setWhatsThis(int column, const QString & whatsThis)`: Sets the `What's This?` help for the given `column` to `whatsThis`.
- `QSize QTreeWidgetItem::sizeHint(int column) const`: Returns the size hint set for the tree item in the given `column`.
- `void QTreeWidgetItem::sortChildren(int column, Qt::SortOrder order)`: Sorts the children of the item using the given `order`, by the values in the given `column`. **Note**: This function does nothing if the item is not associated with a `QTreeWidget`.
- `QString QTreeWidgetItem::statusTip(int column) const`: Returns the status tip for the contents of the given `column`.
- `QTreeWidgetItem * QTreeWidgetItem::takeChild(int index)`: Removes the item at `index` and returns it, otherwise return `0`.
- `QList<QTreeWidgetItem *> QTreeWidgetItem::takeChildren()`: Removes the list of children and returns it, otherwise returns an empty list.
- `QString QTreeWidgetItem::text(int column) const`: Returns the text in the specified `column`.
- `int QTreeWidgetItem::textAlignment(int column) const`: Returns the text alignment for the label in the given `column`.
- `QString QTreeWidgetItem::toolTip(int column) const`: Returns the tool tip for the given `column`.
- `QTreeWidget * QTreeWidgetItem::treeWidget() const`: Returns the tree widget that contains the item.
- `int QTreeWidgetItem::type() const`: Returns the type passed to the `QTreeWidgetItem` constructor.
- `QString QTreeWidgetItem::whatsThis(int column) const`: Returns the `What's This?` help for the contents of the given `column`.
- `void QTreeWidgetItem::write(QDataStream & out) const [virtual]`: Writes the item to stream `out`. This only writes data from one single item.
- `bool QTreeWidgetItem::operator<(const QTreeWidgetItem & other) const [virtual]`: Returns `true` if the text in the item is less than the text in the `other` item, otherwise returns `false`.
- `QTreeWidgetItem & QTreeWidgetItem::operator=(const QTreeWidgetItem & other)`: Assigns `other's` data and flags to this item. Note that `type()` and `treeWidget()` are not copied. This function is useful when reimplementing `clone()`.

### Related Non-Members

- `QDataStream & operator<<(QDataStream & out, const QTreeWidgetItem & item)`: Writes the tree widget item `item` to stream `out`. This operator uses `QTreeWidgetItem::write()`.
- `QDataStream & operator>>(QDataStream & in, QTreeWidgetItem & item)`: Reads a tree widget item from stream `in` into `item`. This operator uses `QTreeWidgetItem::read()`.