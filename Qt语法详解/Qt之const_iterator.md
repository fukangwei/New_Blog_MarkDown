---
title: Qt之const_iterator
categories: Qt语法详解
date: 2019-01-22 15:41:04
---
&emsp;&emsp;The `QList::const_iterator` class provides an `STL-style` const iterator for `QList` and `QQueue`. The header file is `const_iterator`.<!--more-->

### Public Functions

 Return            | Function
-------------------|---------
                   | `const_iterator()`
                   | `const_iterator(const const_iterator & other)`
                   | `const_iterator(const iterator & other)`
`bool`             | `operator!=(const const_iterator & other) const`
`const T &`        | `operator*() const`
`const_iterator`   | `operator+(int j) const`
`const_iterator &` | `operator++()`
`const_iterator`   | `operator++(int)`
`const_iterator &` | `operator+=(int j)`
`const_iterator`   | `operator-(int j) const`
`int`              | `operator-(const_iterator other) const`
`const_iterator &` | `operator--()`
`const_iterator`   | `operator--(int)`
`const_iterator &` | `operator-=(int j)`
`const T *`        | `operator->() const`
`bool`             | `operator<(const const_iterator & other) const`
`bool`             | `operator<=(const const_iterator & other) const`
`bool`             | `operator==(const const_iterator & other) const`
`bool`             | `operator>(const const_iterator & other) const`
`bool`             | `operator>=(const const_iterator & other) const`
`const T &`        | `operator[](int j) const`

### Detailed Description

&emsp;&emsp;The `QList::const_iterator` class provides an `STL-style` const iterator for `QList` and `QQueue`.
&emsp;&emsp;`QList` provides both `STL-style` iterators and `Java-style` iterators. The `STL-style` iterators are more `low-level` and more cumbersome to use; on the other hand, they are slightly faster and, for developers who already know `STL`, have the advantage of familiarity.
&emsp;&emsp;`QList<T>::const_iterator` allows you to iterate over a `QList<T>` (or a `QQueue<T>`). If you want to modify the `QList` as you iterate over it, use `QList::iterator` instead. It is generally good practice to use `QList::const_iterator` on a `non-const QList` as well, unless you need to change the `QList` through the iterator. Const iterators are slightly faster, and can improve code readability.
&emsp;&emsp;The default `QList::const_iterator` constructor creates an uninitialized iterator. You must initialize it using a `QList` function like `QList::constBegin()`, `QList::constEnd()`, or `QList::insert()` before you can start iterating. Here's a typical loop that prints all the items stored in a list:

``` cpp
QList<QString> list;
list.append ( "January" );
list.append ( "February" );
list.append ( "December" );

QList<QString>::const_iterator i;

for ( i = list.constBegin(); i != list.constEnd(); ++i ) {
    cout << *i << endl;
}
```

&emsp;&emsp;Most `QList` functions accept an integer index rather than an iterator. For that reason, iterators are rarely useful in connection with `QList`. One place where `STL-style` iterators do make sense is as arguments to generic algorithms.
&emsp;&emsp;For example, here's how to delete all the widgets stored in a `QList<QWidget *>`:

``` cpp
QList<QWidget *> list;
qDeleteAll ( list.constBegin(), list.constEnd() );
```

&emsp;&emsp;Multiple iterators can be used on the same list. However, be aware that any `non-const` function call performed on the `QList` will render all existing iterators undefined. If you need to keep iterators over a long period of time, we recommend that you use `QLinkedList` rather than `QList`.

### Member Function Documentation

- `const_iterator::const_iterator()`: Constructs an uninitialized iterator. Functions like `operator*()` and `operator++()` should not be called on an uninitialized iterator. Use `operator=()` to assign a value to it before using it.
- `const_iterator::const_iterator(const const_iterator & other)`: Constructs a copy of `other`.
- `const_iterator::const_iterator(const iterator & other)`: Constructs a copy of `other`.
- `bool const_iterator::operator!=(const const_iterator & other) const`: Returns `true` if `other` points to a different item than this iterator; otherwise returns `false`.
- `const T & const_iterator::operator*() const`: Returns the current item.
- `const_iterator const_iterator::operator+(int j) const`: Returns an iterator to the item at `j` positions forward from this iterator (If `j` is negative, the iterator goes backward).
- `const_iterator & const_iterator::operator++()`: The prefix `++` operator (`++it`) advances the iterator to the next item in the list and returns an iterator to the new current item. Calling this function on `QList::end()` leads to undefined results.
- `const_iterator const_iterator::operator++(int)`: This is an overloaded function. The postfix `++` operator (`it++`) advances the iterator to the next item in the list and returns an iterator to the previously current item.
- `const_iterator & const_iterator::operator+=(int j)`: Advances the iterator by `j` items (If `j` is negative, the iterator goes backward).
- `const_iterator const_iterator::operator-(int j) const`: Returns an iterator to the item at `j` positions backward from this iterator (If `j` is negative, the iterator goes forward).
- `int const_iterator::operator-(const_iterator other) const`: Returns the number of items between the item pointed to by `other` and the item pointed to by this iterator.
- `const_iterator & const_iterator::operator--()`: The prefix `--` operator (`--it`) makes the preceding item current and returns an iterator to the new current item. Calling this function on `QList::begin()` leads to undefined results.
- `const_iterator const_iterator::operator--(int)`: This is an overloaded function. The postfix `--` operator (`it--`) makes the preceding item current and returns an iterator to the previously current item.
- `const_iterator & const_iterator::operator-=(int j)`: Makes the iterator go back by `j` items (If `j` is negative, the iterator goes forward).
- `const T * const_iterator::operator->() const`: Returns a pointer to the current item.
- `bool const_iterator::operator<(const const_iterator & other) const`: Returns `true` if the item pointed to by this iterator is less than the item pointed to by the `other` iterator.
- `bool const_iterator::operator<=(const const_iterator & other) const`: Returns `true` if the item pointed to by this iterator is less than or equal to the item pointed to by the `other` iterator.
- `bool const_iterator::operator==(const const_iterator & other) const`: Returns `true` if `other` points to the same item as this iterator; otherwise returns `false`.
- `bool const_iterator::operator>(const const_iterator & other) const`: Returns `true` if the item pointed to by this iterator is greater than the item pointed to by the `other` iterator.
- `bool const_iterator::operator>=(const const_iterator & other) const`: Returns `true` if the item pointed to by this iterator is greater than or equal to the item pointed to by the `other` iterator.
- `const T & const_iterator::operator[](int j) const`: Returns the item at position `*this + j`. This function is provided to make `QList` iterators behave like `C++` pointers.