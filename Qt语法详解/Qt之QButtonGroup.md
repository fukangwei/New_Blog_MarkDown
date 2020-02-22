---
title: Qt之QButtonGroup
categories: Qt语法详解
date: 2019-01-22 17:30:34
---
&emsp;&emsp;The `QButtonGroup` class provides a container to organize groups of button widgets.<!--more-->

Header         | Inherits
---------------|---------
`QButtonGroup` | `QObject`

### Public Functions

Return                     | Function
---------------------------|---------
                           | `QButtonGroup(QObject * parent = 0)`
                           | `~QButtonGroup()`
`void`                     | `addButton(QAbstractButton * button)`
`void`                     | `addButton(QAbstractButton * button, int id)`
`QAbstractButton *`        | `button(int id) const`
`QList<QAbstractButton *>` | `buttons() const`
`QAbstractButton *`        | `checkedButton() const`
`int`                      | `checkedId() const`
`bool`                     | `exclusive() const`
`int`                      | `id(QAbstractButton * button) const`
`void`                     | `removeButton(QAbstractButton * button)`
`void`                     | `setExclusive(bool)`
`void`                     | `setId(QAbstractButton * button, int id)`

### Signals

- `void buttonClicked ( QAbstractButton *button );`
- `void buttonClicked ( int id );`
- `void buttonPressed ( QAbstractButton *button );`
- `void buttonPressed ( int id );`
- `void buttonReleased ( QAbstractButton *button );`
- `void buttonReleased ( int id );`

### Detailed Description

&emsp;&emsp;The `QButtonGroup` class provides a container to organize groups of button widgets.
&emsp;&emsp;`QButtonGroup` provides an abstract container into which button widgets can be placed. It does not provide a visual representation of this container (see `QGroupBox` for a container widget), but instead manages the states of each of the buttons in the group.
&emsp;&emsp;An exclusive button group switches off all checkable (toggle) buttons except the one that was clicked. By default, a button group is exclusive. The buttons in a button group are usually checkable `QPushButton's`, `QCheckBoxes` (normally for `non-exclusive` button groups), or `QRadioButtons`. If you create an exclusive button group, you should ensure that one of the buttons in the group is initially checked; otherwise, the group will initially be in a state where no buttons are checked.
&emsp;&emsp;A button is added to the group with `addButton()`. It can be removed from the group with `removeButton()`. If the group is exclusive, the currently checked button is available as `checkedButton()`. If a button is clicked the `buttonClicked()` signal is emitted. For a checkable button in an exclusive group this means that the button was checked. The list of buttons in the group is returned by `buttons()`.
&emsp;&emsp;In addition, `QButtonGroup` can map between integers and buttons. You can assign an integer id to a button with `setId()`, and retrieve it with `id()`. The id of the currently checked button is available with `checkedId()`, and there is an overloaded signal `buttonClicked()` which emits the id of the button. The id `-1` is reserved by `QButtonGroup` to mean `no such button`. The purpose of the mapping mechanism is to simplify the representation of enum values in a user interface.

### Member Function Documentation

- `QButtonGroup::QButtonGroup(QObject * parent = 0)`: Constructs a new, empty button group with the given `parent`.
- `QButtonGroup::~QButtonGroup()`: Destroys the button group.
- `void QButtonGroup::addButton(QAbstractButton * button)`: Adds the given `button` to the end of the group's internal list of buttons. An id will be assigned to the button by this `QButtonGroup`. Automatically assigned ids are guaranteed to be negative, starting with `-2`. If you are also assigning your own ids, use positive values to avoid conflicts.
- `void QButtonGroup::addButton(QAbstractButton * button, int id)`: Adds the given `button` to the button group, with the given `id`. It is recommended to assign only positive ids.
- `QAbstractButton * QButtonGroup::button(int id) const`: Returns the button with the specified `id`, or `0` if no such button exists.
- `void QButtonGroup::buttonClicked(QAbstractButton * button) [signal]`: This signal is emitted when the given `button` is clicked. A button is clicked when it is first pressed and then released, when its shortcut key is typed, or programmatically when `QAbstractButton::click()` or `QAbstractButton::animateClick()` is called. **Note**: Signal `buttonClicked` is overloaded in this class. To connect to this one using the function pointer syntax, you must specify the signal type in a static cast:

``` cpp
connect ( buttonGroup, static_cast<void ( QButtonGroup::* ) ( QAbstractButton * ) > \
        ( &QButtonGroup::buttonClicked ), [ = ] ( QAbstractButton *button ) { /* ... */ } );
```

- `void QButtonGroup::buttonClicked(int id) [signal]`: This signal is emitted when a button with the given `id` is clicked. **Note**: Signal `buttonClicked` is overloaded in this class. To connect to this one using the function pointer syntax, you must specify the signal type in a static cast:

``` cpp
connect ( buttonGroup, static_cast<void ( QButtonGroup::* ) ( int ) > \
        ( &QButtonGroup::buttonClicked ), [ = ] ( int id ) { /* ... */ } );
```

- `void QButtonGroup::buttonPressed(QAbstractButton * button) [signal]`: This signal is emitted when the given `button` is pressed down. **Note**: Signal `buttonPressed` is overloaded in this class. To connect to this one using the function pointer syntax, you must specify the signal type in a static cast:

``` cpp
connect ( buttonGroup, static_cast<void ( QButtonGroup::* ) ( QAbstractButton * ) > \
        ( &QButtonGroup::buttonPressed ), [ = ] ( QAbstractButton *button ) { /* ... */ } );
```

- `void QButtonGroup::buttonPressed(int id) [signal]`: This signal is emitted when a button with the given `id` is pressed down. **Note**: Signal `buttonPressed` is overloaded in this class. To connect to this one using the function pointer syntax, you must specify the signal type in a static cast:

``` cpp
connect ( buttonGroup, static_cast<void ( QButtonGroup::* ) ( int ) > \
        ( &QButtonGroup::buttonPressed ), [ = ] ( int id ) { /* ... */ } );
```

- `void QButtonGroup::buttonReleased(QAbstractButton * button) [signal]`: This signal is emitted when the given `button` is released. **Note**: Signal `buttonReleased` is overloaded in this class. To connect to this one using the function pointer syntax, you must specify the signal type in a static cast:

``` cpp
connect ( buttonGroup, static_cast<void ( QButtonGroup::* ) ( QAbstractButton * ) > \
        ( &QButtonGroup::buttonReleased ), [ = ] ( QAbstractButton *button ) { /* ... */ } );
```

- `void QButtonGroup::buttonReleased(int id) [signal]`: This signal is emitted when a button with the given `id` is released. **Note**: Signal `buttonReleased` is overloaded in this class. To connect to this one using the function pointer syntax, you must specify the signal type in a static cast:

``` cpp
connect ( buttonGroup, static_cast<void ( QButtonGroup::* ) ( int ) > \
        ( &QButtonGroup::buttonReleased ), [ = ] ( int id ) { /* ... */ } );
```

- `QList<QAbstractButton *> QButtonGroup::buttons() const`: Returns the list of this groups's buttons. This may be empty.
- `QAbstractButton * QButtonGroup::checkedButton() const`: Returns the button group's checked button, or `0` if no buttons are checked.
- `int QButtonGroup::checkedId() const`: Returns the id of the `checkedButton()`, or `-1` if no button is checked.
- `int QButtonGroup::id(QAbstractButton * button) const`: Returns the id for the specified `button`, or `-1` if no such button exists.
- `void QButtonGroup::removeButton(QAbstractButton * button)`: Removes the given `button` from the button group.
- `void QButtonGroup::setId(QAbstractButton * button, int id)`: Sets the `id` for the specified `button`. Note that `id` can not be `-1`.