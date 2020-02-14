---
title: Qt之QActionGroup
categories: Qt语法详解
abbrlink: 859caffc
date: 2019-01-03 17:21:25
---
&emsp;&emsp;The `QActionGroup` class groups actions together.<!--more-->

Header         | Inherits
---------------|---------
`QActionGroup` | `QObject`

### Properties

- `enabled`: `bool`
- `exclusive`: `bool`
- `visible`: `bool`

### Public Functions

Return             | Function
-------------------|----------
                   | `QActionGroup(QObject * parent)`
                   | `~QActionGroup()`
`QList<QAction *>` | `actions() const`
`QAction *`        | `addAction(QAction * action)`
`QAction *`        | `addAction(const QString & text)`
`QAction *`        | `addAction(const QIcon & icon, const QString & text)`
`QAction *`        | `checkedAction() const`
`bool`             | `isEnabled() const`
`bool`             | `isExclusive() const`
`bool`             | `isVisible() const`
`void`             | `removeAction(QAction * action)`

### Public Slots

- `void setDisabled ( bool );`
- `void setEnabled ( bool );`
- `void setExclusive ( bool );`
- `void setVisible ( bool );`

### Signals

- `void hovered ( QAction *action );`
- `void triggered ( QAction *action );`

### Detailed Description

&emsp;&emsp;The `QActionGroup` class groups actions together.
&emsp;&emsp;In some situations it is useful to group `QAction` objects together. For example, if you have a `Left` `Align` action, a `Right` `Align` action, a `Justify` action, and a `Center` action, only one of these actions should be active at any one time. One simple way of achieving this is to group the actions together in an action group.
&emsp;&emsp;Here's a example:

``` cpp
alignmentGroup = new QActionGroup ( this );
alignmentGroup->addAction ( leftAlignAct );
alignmentGroup->addAction ( rightAlignAct );
alignmentGroup->addAction ( justifyAct );
alignmentGroup->addAction ( centerAct );
leftAlignAct->setChecked ( true );
```

Here we create a new action group. Since the action group is exclusive by default, only one of the actions in the group is checked at any one time.
&emsp;&emsp;A `QActionGroup` emits an `triggered()` signal when one of its actions is chosen. Each action in an action group emits its `triggered()` signal as usual.
&emsp;&emsp;As stated above, an action group is exclusive by default; it ensures that only one checkable action is active at any one time. If you want to group checkable actions without making them exclusive, you can turn of exclusiveness by calling `setExclusive(false)`.
&emsp;&emsp;Actions can be added to an action group using `addAction()`, but it is usually more convenient to specify a group when creating actions; this ensures that actions are automatically created with a parent. Actions can be visually separated from each other by adding a separator action to the group; create an action and use QAction's `setSeparator()` function to make it considered a separator. Action groups are added to widgets with the `QWidget::addActions()` function.

### Property Documentation

&emsp;&emsp;`enabled`(`bool`): This property holds whether the action group is `enabled`. Each action in the group will be `enabled` or `disabled` unless it has been explicitly `disabled`. Access functions:

``` cpp
bool isEnabled() const;
void setEnabled ( bool );
```

&emsp;&emsp;`exclusive`(`bool`): This property holds whether the action group does `exclusive` checking. If `exclusive` is `true`, only one checkable action in the action group can ever be `active` at any time. If the user chooses another checkable action in the group, the one they chose becomes `active` and the one that was `active` becomes `inactive`. Access functions:

``` cpp
bool isExclusive() const;
void setExclusive ( bool );
```

&emsp;&emsp;`visible`(`bool`): This property holds whether the action group is `visible`. Each action in the action group will match the `visible` state of this group unless it has been explicitly `hidden`. Access functions:

``` cpp
bool isVisible() const;
void setVisible ( bool );
```

### Member Function Documentation

- `QActionGroup::QActionGroup(QObject * parent)`: Constructs an action group for the `parent` object. The action group is `exclusive` by default. Call `setExclusive(false)` to make the action group `non-exclusive`.
- `QActionGroup::~QActionGroup()`: Destroys the action group.
- `QList<QAction *> QActionGroup::actions() const`: Returns the list of this groups's actions. This may be empty.
- `QAction * QActionGroup::addAction(QAction * action)`: Adds the `action` to this group, and returns it. Normally an action is added to a group by creating it with the group as its parent, so this function is not usually used.
- `QAction * QActionGroup::addAction(const QString & text)`: Creates and returns an action with `text`. The newly created action is a child of this action group. Normally an action is added to a group by creating it with the group as parent, so this function is not usually used.
- `QAction * QActionGroup::addAction(const QIcon & icon, const QString & text)`: Creates and returns an action with `text` and an `icon`. The newly created action is a child of this action group. Normally an action is added to a group by creating it with the group as its parent, so this function is not usually used.
- `QAction * QActionGroup::checkedAction() const`: Returns the currently `checked` action in the group, or `0` if `none` are `checked`.
- `void QActionGroup::hovered(QAction * action) [signal]`: This signal is emitted when the given `action` in the action group is highlighted by the user; for example, when the user pauses with the cursor over a menu option, toolbar button, or presses an action's shortcut key combination.
- `void QActionGroup::removeAction(QAction * action)`: Removes the `action` from this group. The `action` will have no parent as a result.
- `void QActionGroup::setDisabled(bool b) [slot]`: This is a convenience function for the `enabled` property, that is useful for `signals <--> slots` connections. If `b` is `true`, the action group is `disabled`; otherwise it is `enabled`.
- `void QActionGroup::triggered(QAction * action) [signal]`: This signal is emitted when the given `action` in the action group is activated by the user; for example, when the user clicks a menu option, toolbar button, or presses an action's shortcut key combination. Connect to this signal for command actions.