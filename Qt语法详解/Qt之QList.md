---
title: Qt之QList
date: 2019-01-31 19:46:43
categories: Qt语法详解
---
&emsp;&emsp;The `QList` class is a template class that provides lists.

Header   | Inherited By
---------|-------------
`QList`  | `QItemSelection`, `QQueue`, `QSignalSpy`, `QStringList` and `QTestEventList`

**Note**: All functions in this class are reentrant.

### Public Functions

Return           | Function
-----------------|---------
                 | `QList()`
                 | `QList(const QList<T> & other)`
                 | `QList(std::initializer_list<T> args)`
                 | `~QList()`
`void`           | `append(const T & value)`
`void`           | `append(const QList<T> & value)`
`const T &`      | `at(int i) const`
`T &`            | `back()`
`const T &`      | `back() const`
`iterator`       | `begin()`
`const_iterator` | `begin() const`
`void`           | `clear()`
`const_iterator` | `constBegin() const`
`const_iterator` | `constEnd() const`
`bool`           | `contains(const T & value) const`
`int`            | `count(const T & value) const`
`int`            | `count() const`
`bool`           | `empty() const`
`iterator`       | `end()`
`const_iterator` | `end() const`
`bool`           | `endsWith(const T & value) const`
`iterator`       | `erase(iterator pos)`
`iterator`       | `erase(iterator begin, iterator end)`
`T &`            | `first()`
`const T &`      | `first() const`
`T &`            | `front()`
`const T &`      | `front() const`
`int`            | `indexOf(const T & value, int from = 0) const`
`void`           | `insert(int i, const T & value)`
`iterator`       | `insert(iterator before, const T & value)`
`bool`           | `isEmpty() const`
`T &`            | `last()`
`const T &`      | `last() const`
`int`            | `lastIndexOf(const T & value, int from = -1) const`
`int`            | `length() const`
`QList<T>`       | `mid(int pos, int length = -1) const`
`void`           | `move(int from, int to)`
`void`           | `pop_back()`
`void`           | `pop_front()`
`void`           | `prepend(const T & value)`
`void`           | `push_back(const T & value)`
`void`           | `push_front(const T & value)`
`int`            | `removeAll(const T & value)`
`void`           | `removeAt(int i)`
`void`           | `removeFirst()`
`void`           | `removeLast()`
`bool`           | `removeOne(const T & value)`
`void`           | `replace(int i, const T & value)`
`void`           | `reserve(int alloc)`
`int`            | `size() const`
`bool`           | `startsWith(const T & value) const`
`void`           | `swap(QList<T> & other)`
`void`           | `swap(int i, int j)`
`T`              | `takeAt(int i)`
`T`              | `takeFirst()`
`T`              | `takeLast()`
`QSet<T>`        | `toSet() const`
`std::list<T>`   | `toStdList() const`
`QVector<T>`     | `toVector() const`
`T`              | `value(int i) const`
`T`              | `value(int i, const T & defaultValue) const`
`bool`           | `operator!=(const QList<T> & other) const`
`QList<T>`       | `operator+(const QList<T> & other) const`
`QList<T> &`     | `operator+=(const QList<T> & other)`
`QList<T> &`     | `operator+=(const T & value)`
`QList<T> &`     | `operator<<(const QList<T> & other)`
`QList<T> &`     | `operator<<(const T & value)`
`QList<T> &`     | `operator=(const QList<T> & other)`
`QList &`        | `operator=(QList && other)`
`bool`           | `operator==(const QList<T> & other) const`
`T &`            | `operator[](int i)`
`const T &`      | `operator[](int i) const`

### Static Public Members

Return     | Function
-----------|---------
`QList<T>` | `fromSet ( const QSet<T> &set );`
`QList<T>` | `fromStdList ( const std::list<T> &list );`
`QList<T>` | `fromVector ( const QVector<T> &vector );`

### Related Non-Members

Return          | Function
----------------|---------
`QDataStream &` | `operator<< ( QDataStream &out, const QList<T> &list );`
`QDataStream &` | `operator>> ( QDataStream &in, QList<T> &list );`

### Detailed Description

&emsp;&emsp;The `QList` class is a template class that provides lists.
&emsp;&emsp;`QList<T>` is one of `Qt's` generic container classes. It stores a list of values and provides fast `index-based` access as well as fast insertions and removals.
&emsp;&emsp;`QList<T>`, `QLinkedList<T>`, and `QVector<T>` provide similar functionality. Here's an overview:
&emsp;&emsp;For most purposes, `QList` is the right class to use. Its `index-based` `API` is more convenient than `QLinkedList's` `iterator-based` `API`, and it is usually faster than `QVector` because of the way it stores its items in memory. It also expands to less code in your executable.
&emsp;&emsp;If you need a real linked list, with guarantees of constant time insertions in the middle of the list and iterators to items rather than indexes, use `QLinkedList`.
&emsp;&emsp;If you want the items to occupy adjacent memory positions, use `QVector`.
&emsp;&emsp;Internally, `QList<T>` is represented as an array of pointers to items of type `T`. If `T` is itself a pointer type or a basic type that is no larger than a pointer, or if `T` is one of Qt's shared classes, then `QList<T>` stores the items directly in the pointer array. For lists under a thousand items, this array representation allows for very fast insertions in the middle, and it allows `index-based` access. Furthermore, operations like `prepend()` and `append()` are very fast, because `QList` preallocates memory at both ends of its internal array. Note, however, that for unshared list items that are larger than a pointer, each append or insert of a new item requires allocating the new item on the heap, and this per item allocation might make `QVector` a better choice in cases that do lots of appending or inserting, since `QVector` allocates memory for its items in a single heap allocation.
&emsp;&emsp;Note that the internal array only ever gets bigger over the life of the list. It never shrinks. The internal array is deallocated by the destructor, by `clear()`, and by the assignment operator, when one list is assigned to another.
&emsp;&emsp;Here's an example of a `QList` that stores integers and a `QList` that stores `QDate` values:

``` cpp
QList<int> integerList;
QList<QDate> dateList;
```

&emsp;&emsp;Qt includes a `QStringList` class that inherits `QList<QString>` and adds a convenience function `QStringList::join()` (`QString::split()` creates `QStringLists` from strings).
&emsp;&emsp;`QList` stores a list of items. The default constructor creates an empty list. To insert items into the list, you can use `operator<<()`:

``` cpp
QList<QString> list;
list << "one" << "two" << "three"; /* list: ["one", "two", "three"] */
```

&emsp;&emsp;`QList` provides these basic functions to add, move, and remove items: `insert()`, `replace()`, `removeAt()`, `move()`, and `swap()`. In addition, it provides the following convenience functions: `append()`, `prepend()`, `removeFirst()` and `removeLast()`.
&emsp;&emsp;`QList` uses `0-based` indexes, just like `C++` arrays. To access the item at a particular index position, you can use `operator[]()`. On `non-const` lists, `operator[]()` returns a reference to the item and can be used on the left side of an assignment:

``` cpp
if ( list[0] == "Bob" ) {
    list[0] = "Robert";
}
```

&emsp;&emsp;Because `QList` is implemented as an array of pointers, this operation is very fast (constant time). For `read-only` access, an alternative syntax is to use `at()`:

``` cpp
for ( int i = 0; i < list.size(); ++i ) {
    if ( list.at ( i ) == "Jane" ) {
        cout << "Found Jane at position " << i << endl;
    }
}
```

&emsp;&emsp;`at()` can be faster than `operator[]()`, because it never causes a deep copy to occur.
&emsp;&emsp;A common requirement is to remove an item from a list and do something with it. For this, `QList` provides `takeAt()`, `takeFirst()`, and `takeLast()`. Here's a loop that removes the items from a list one at a time and calls delete on them:

``` cpp
QList<QWidget *> list;
​
while ( !list.isEmpty() ) {
    delete list.takeFirst();
}
```

&emsp;&emsp;Inserting and removing items at either ends of the list is very fast (constant time in most cases), because `QList` preallocates extra space on both sides of its internal buffer to allow for fast growth at both ends of the list.
&emsp;&emsp;If you want to find all occurrences of a particular value in a list, use `indexOf()` or `lastIndexOf()`. The former searches forward starting from a given index position, the latter searches backward. Both return the index of a matching item if they find it; otherwise, they return `-1`. For example:

``` cpp
int i = list.indexOf ( "Jane" );
​
if ( i != -1 ) {
    cout << "First occurrence of Jane is at position " << i << endl;
}
```

&emsp;&emsp;If you simply want to check whether a list contains a particular value, use `contains()`. If you want to find out how many times a particular value occurs in the list, use `count()`. If you want to replace all occurrences of a particular value with another, use `replace()`.
&emsp;&emsp;`QList's` value type must be an assignable data type. This covers most data types that are commonly used, but the compiler won't let you, for example, store a `QWidget` as a value; instead, store a `QWidget *`. A few functions have additional requirements; for example, `indexOf()` and `lastIndexOf()` expect the value type to support `operator==()`. These requirements are documented on a `per-function` basis.
&emsp;&emsp;Like the other container classes, `QList` provides `Java-style` iterators (`QListIterator` and `QMutableListIterator`) and `STL-style` iterators (`QList::const_iterator` and `QList::iterator`). In practice, these are rarely used, because you can use indexes into the `QList`. `QList` is implemented in such a way that direct `index-based` access is just as fast as using iterators.
&emsp;&emsp;`QList` does not support inserting, prepending, appending or replacing with references to its own values. Doing so will cause your application to abort with an error message.
&emsp;&emsp;To make `QList` as efficient as possible, its member functions don't validate their input before using it. Except for `isEmpty()`, member functions always assume the list is not empty. Member functions that take index values as parameters always assume their index value parameters are in the valid range. This means `QList` member functions can fail. If you define `QT_NO_DEBUG` when you compile, failures will not be detected. If you don't define `QT_NO_DEBUG`, failures will be detected using `Q_ASSERT()` or `Q_ASSERT_X()` with an appropriate message.
&emsp;&emsp;To avoid failures when your list can be empty, call `isEmpty()` before calling other member functions. If you must pass an index value that might not be in the valid range, check that it is less than the value returned by `size()` but not less than `0`.

### Member Type Documentation

- typedef `QList::ConstIterator`: `Qt-style` synonym for `QList::const_iterator`.
- typedef `QList::Iterator`: `Qt-style` synonym for `QList::iterator`.
- typedef `QList::const_pointer`: Typedef for `const T *`. Provided for `STL` compatibility.
- typedef `QList::const_reference`: Typedef for `const T &`. Provided for `STL` compatibility.
- typedef `QList::difference_type`: Typedef for `ptrdiff_t`. Provided for `STL` compatibility.
- typedef `QList::pointer`: Typedef for `T *`. Provided for `STL` compatibility.
- typedef `QList::reference`: Typedef for `T &`. Provided for `STL` compatibility.
- typedef `QList::size_type`: Typedef for `int`. Provided for `STL` compatibility.
- typedef `QList::value_type`: Typedef for `T`. Provided for `STL` compatibility.

### Member Function Documentation

- `QList::QList()`: Constructs an empty list.
- `QList::QList(const QList<T> & other)`: Constructs a copy of `other`. This operation takes constant time, because `QList` is implicitly shared. This makes returning a `QList` from a function very fast. If a shared instance is modified, it will be copied (`copy-on-write`), and that takes linear time.
- `QList::QList(std::initializer_list<T> args)`: Construct a list from the `std::initializer_list specified` by `args`. This constructor is only enabled if the compiler supports `C++0x`.
- `QList::~QList()`: Destroys the list. References to the values in the list and all iterators of this list become invalid.
- `void QList::append(const T & value)`: Inserts `value` at the end of the list. Example:

``` cpp
QList<QString> list;
list.append ( "one" );
list.append ( "two" );
list.append ( "three" ); /* list: ["one", "two", "three"] */
```

This is the same as `list.insert(size(), value)`. This operation is typically very fast (constant time), because `QList` preallocates extra space on both sides of its internal buffer to allow for fast growth at both ends of the list.

- `void QList::append(const QList<T> & value)`: This is an overloaded function. Appends the items of the `value` list to this list.
- `const T & QList::at(int i) const`: Returns the item at index position `i` in the list. `i` must be a valid index position in the list (i.e., `0 <= i < size()`). This function is very fast (constant time).
- `T & QList::back()`: This function is provided for `STL` compatibility. It is equivalent to `last()`. The list must not be empty. If the list can be empty, call `isEmpty()` before calling this function.
- `const T & QList::back() const`: This is an overloaded function.
- `iterator QList::begin()`: Returns an `STL-style` iterator pointing to the first item in the list.
- `const_iterator QList::begin() const`: This is an overloaded function.
- `void QList::clear()`: Removes all items from the list.
- `const_iterator QList::constBegin() const`: Returns a const `STL-style` iterator pointing to the first item in the list.
- `const_iterator QList::constEnd() const`: Returns a const `STL-style` iterator pointing to the imaginary item after the last item in the list.
- `bool QList::contains(const T & value) const`: Returns `true` if the list contains an occurrence of `value`; otherwise returns `false`. This function requires the value type to have an implementation of `operator==()`.
- `int QList::count(const T & value) const`: Returns the number of occurrences of `value` in the list. This function requires the value type to have an implementation of `operator==()`.
- `int QList::count() const`: Returns the number of items in the list. This is effectively the same as `size()`.
- `bool QList::empty() const`: This function is provided for `STL` compatibility. It is equivalent to `isEmpty()` and returns `true` if the list is empty.
- `iterator QList::end()`: Returns an `STL-style` iterator pointing to the imaginary item after the last item in the list.
- `const_iterator QList::end() const`: This is an overloaded function.
- `bool QList::endsWith(const T & value) const`: Returns `true` if this list is not empty and its last item is equal to `value`; otherwise returns `false`.
- `iterator QList::erase(iterator pos)`: Removes the item associated with the iterator `pos` from the list, and returns an iterator to the next item in the list (which may be `end()`).
- `iterator QList::erase(iterator begin, iterator end)`: This is an overloaded function. Removes all the items from `begin` up to (but not including) `end`. Returns an iterator to the same item that end referred to before the call.
- `T & QList::first()`: Returns a reference to the first item in the list. The list must not be empty. If the list can be empty, call `isEmpty()` before calling this function.
- `const T & QList::first() const`: This is an overloaded function.
- `QList<T> QList::fromSet(const QSet<T> & set) [static]`: Returns a `QList` object with the data contained in `set`. The order of the elements in the `QList` is undefined. Example:

``` cpp
QSet<int> set;
set << 20 << 30 << 40 << ... << 70;
​
QList<int> list = QList<int>::fromSet ( set );
qSort ( list );
```

- `QList<T> QList::fromStdList(const std::list<T> & list) [static]`: Returns a `QList` object with the data contained in `list`. The order of the elements in the `QList` is the same as in `list`. Example:

``` cpp
std::list<double> stdlist;
list.push_back ( 1.2 );
list.push_back ( 0.5 );
list.push_back ( 3.14 );
​
QList<double> list = QList<double>::fromStdList ( stdlist );
```

- `QList<T> QList::fromVector(const QVector<T> & vector) [static]`: Returns a `QList` object with the data contained in `vector`. Example:

``` cpp
QVector<double> vect;
vect << 20.0 << 30.0 << 40.0 << 50.0;
​
QList<double> list = QVector<T>::fromVector ( vect ); /* list: [20.0, 30.0, 40.0, 50.0] */
```

- `T & QList::front()`: This function is provided for `STL` compatibility. It is equivalent to `first()`. The list must not be empty. If the list can be empty, call `isEmpty()` before calling this function.
- `const T & QList::front() const`: This is an overloaded function.
- `int QList::indexOf(const T & value, int from = 0) const`: Returns the index position of the first occurrence of `value` in the list, searching forward from index position `from`. Returns `-1` if no item matched. Example:

``` cpp
QList<QString> list;
list << "A" << "B" << "C" << "B" << "A";
list.indexOf ( "B" ); /* returns 1 */
list.indexOf ( "B", 1 ); /* returns 1 */
list.indexOf ( "B", 2 ); /* returns 3 */
list.indexOf ( "X" ); /* returns -1 */
```

This function requires the value type to have an implementation of `operator==()`. Note that `QList` uses `0-based` indexes, just like `C++` arrays. Negative indexes are not supported with the exception of the value mentioned above.

- `void QList::insert(int i, const T & value)`: Inserts `value` at index position `i` in the list. If `i` is `0`, the `value` is prepended to the list. If `i` is `size()`, the `value` is appended to the list. Example:

``` cpp
QList<QString> list;
list << "alpha" << "beta" << "delta";
list.insert ( 2, "gamma" ); /* list: ["alpha", "beta", "gamma", "delta"] */
```

- `iterator QList::insert(iterator before, const T & value)`: This is an overloaded function. Inserts `value` in front of the item pointed to by the iterator `before`. Returns an iterator pointing at the inserted item. Note that the iterator passed to the function will be invalid after the call; the returned iterator should be used instead.
- `bool QList::isEmpty() const`: Returns `true` if the list contains no items; otherwise returns `false`.
- `T & QList::last()`: Returns a reference to the last item in the list. The list must not be empty. If the list can be empty, call `isEmpty()` before calling this function.
- `const T & QList::last() const`: This is an overloaded function.
- `int QList::lastIndexOf(const T & value, int from = -1) const`: Returns the index position of the last occurrence of `value` in the list, searching backward from index position `from`. If `from` is `-1` (the default), the search starts at the last item. Returns `-1` if no item matched. Example:

``` cpp
QList<QString> list;
list << "A" << "B" << "C" << "B" << "A";
list.lastIndexOf ( "B" ); /* returns 3 */
list.lastIndexOf ( "B", 3 ); /* returns 3 */
list.lastIndexOf ( "B", 2 ); /* returns 1 */
list.lastIndexOf ( "X" ); /* returns -1 */
```

This function requires the value type to have an implementation of `operator==()`. Note that `QList` uses `0-based` indexes, just like `C++` arrays. Negative indexes are not supported with the exception of the value mentioned above.

- `int QList::length() const`: This function is identical to `count()`.
- `QList<T> QList::mid(int pos, int length = -1) const`: Returns a list whose elements are copied from this list, starting at position `pos`. If `length` is `-1` (the default), all elements from `pos` are copied; otherwise `length` elements (or all remaining elements if there are less than `length` elements) are copied.
- `void QList::move(int from, int to)`: Moves the item at index position `from` to index position `to`. Example:

``` cpp
QList<QString> list;
list << "A" << "B" << "C" << "D" << "E" << "F";
list.move ( 1, 4 ); /* list: ["A", "C", "D", "E", "B", "F"] */
```

This is the same as `insert(to, takeAt(from))`. This function assumes that both `from` and `to` are at least `0` but less than `size()`. To avoid failure, test that both `from` and `to` are at least `0` and less than `size()`.

- `void QList::pop_back()`: This function is provided for `STL` compatibility. It is equivalent to `removeLast()`. The list must not be empty. If the list can be empty, call `isEmpty()` before calling this function.
- `void QList::pop_front()`: This function is provided for `STL` compatibility. It is equivalent to `removeFirst()`. The list must not be empty. If the list can be empty, call `isEmpty()` before calling this function.
- `void QList::prepend(const T & value)`: Inserts `value` at the beginning of the list. Example:

``` cpp
QList<QString> list;
list.prepend ( "one" );
list.prepend ( "two" );
list.prepend ( "three" ); /* list: ["three", "two", "one"] */
```

This is the same as `list.insert(0, value)`. This operation is usually very fast (constant time), because `QList` preallocates extra space on both sides of its internal buffer to allow for fast growth at both ends of the list.

- `void QList::push_back(const T & value)`: This function is provided for `STL` compatibility. It is equivalent to `append(value)`.
- `void QList::push_front(const T & value)`: This function is provided for `STL` compatibility. It is equivalent to `prepend(value)`.
- `int QList::removeAll(const T & value)`: Removes all occurrences of `value` in the list and returns the number of entries removed. Example:

``` cpp
QList<QString> list;
list << "sun" << "cloud" << "sun" << "rain";
list.removeAll("sun"); /* list: ["cloud", "rain"] */
```

This function requires the value type to have an implementation of `operator==()`.

- `void QList::removeAt(int i)`: Removes the item at index position `i`. `i` must be a valid index position in the list (i.e., `0 <= i < size()`).
- `void QList::removeFirst()`: Removes the first item in the list. Calling this function is equivalent to calling `removeAt(0)`. The list must not be empty. If the list can be empty, call `isEmpty()` before calling this function.
- `void QList::removeLast()`: Removes the last item in the list. Calling this function is equivalent to calling `removeAt(size() - 1)`. The list must not be empty. If the list can be empty, call `isEmpty()` before calling this function.
- `bool QList::removeOne(const T & value)`: Removes the first occurrence of `value` in the list and returns `true` on success; otherwise returns `false`. Example:

``` cpp
QList<QString> list;
list << "sun" << "cloud" << "sun" << "rain";
list.removeOne("sun"); /* list: ["cloud", ,"sun", "rain"] */
```

This function requires the value type to have an implementation of `operator==()`.

- `void QList::replace(int i, const T & value)`: Replaces the item at index position `i` with `value`. `i` must be a valid index position in the list (i.e., `0 <= i < size()`).
- `void QList::reserve(int alloc)`: Reserve space for `alloc` elements. If `alloc` is smaller than the current size of the list, nothing will happen. Use this function to avoid repetetive reallocation of `QList's` internal data if you can predict how many elements will be appended. Note that the reservation applies only to the internal pointer array.
- `int QList::size() const`: Returns the number of items in the list.
- `bool QList::startsWith(const T & value) const`: Returns `true` if this list is not empty and its first item is equal to `value`; otherwise returns `false`.
- `void QList::swap(QList<T> & other)`: Swaps list `other` with this list. This operation is very fast and never fails.
- `void QList::swap(int i, int j)`: Exchange the item at index position `i` with the item at index position `j`. This function assumes that both `i` and `j` are at least `0` but less than `size()`. To avoid failure, test that both `i` and `j` are at least `0` and less than `size()`. Example:

``` cpp
QList<QString> list;
list << "A" << "B" << "C" << "D" << "E" << "F";
list.swap ( 1, 4 ); /* list: ["A", "E", "C", "D", "B", "F"] */
```

- `T QList::takeAt(int i)`: Removes the item at index position `i` and returns it. `i` must be a valid index position in the list (i.e., `0 <= i < size()`). If you don't use the return value, `removeAt()` is more efficient.
- `T QList::takeFirst()`: Removes the first item in the list and returns it. This is the same as `takeAt(0)`. This function assumes the list is not empty. To avoid failure, call `isEmpty()` before calling this function. This operation takes constant time. If you don't use the return value, `removeFirst()` is more efficient.
- `T QList::takeLast()`: Removes the last item in the list and returns it. This is the same as `takeAt(size() - 1)`. This function assumes the list is not empty. To avoid failure, call `isEmpty()` before calling this function. This operation takes constant time. If you don't use the return value, `removeLast()` is more efficient.
- `QSet<T> QList::toSet() const`: Returns a `QSet` object with the data contained in this `QList`. Since `QSet` doesn't allow duplicates, the resulting `QSet` might be smaller than the original list was. Example:

``` cpp
QStringList list;
list << "Julia" << "Mike" << "Mike" << "Julia" << "Julia";
​
QSet<QString> set = list.toSet();
set.contains ( "Julia" ); /* returns true */
set.contains ( "Mike" ); /* returns true */
set.size(); /* returns 2 */
```

- `std::list<T> QList::toStdList() const`: Returns a `std::list` object with the data contained in this `QList`. Example:

``` cpp
QList<double> list;
list << 1.2 << 0.5 << 3.14;
​
std::list<double> stdlist = list.toStdList();
```

- `QVector<T> QList::toVector() const`: Returns a `QVector` object with the data contained in this `QList`. Example:

``` cpp
QStringList list;
list << "Sven" << "Kim" << "Ola";
​
QVector<QString> vect = list.toVector(); /* vect: ["Sven", "Kim", "Ola"] */
```

- `T QList::value(int i) const`: Returns the value at index position `i` in the list. If the index `i` is out of bounds, the function returns a `default-constructed` value. If you are certain that the index is going to be within bounds, you can use `at()` instead, which is slightly faster.
- `T QList::value(int i, const T & defaultValue) const`: This is an overloaded function. If the index `i` is out of bounds, the function returns `defaultValue`.
- `bool QList::operator!=(const QList<T> & other) const`: Returns `true` if other is not equal to this list; otherwise returns `false`. Two lists are considered equal if they contain the same values in the same order. This function requires the value type to have an implementation of `operator==()`.
- `QList<T> QList::operator+(const QList<T> & other) const`: Returns a list that contains all the items in this list followed by all the items in the `other` list.
- `QList<T> & QList::operator+=(const QList<T> & other)`: Appends the items of the `other` list to this list and returns a reference to this list.
- `QList<T> & QList::operator+=(const T & value)`: This is an overloaded function. Appends `value` to the list.
- `QList<T> & QList::operator<<(const QList<T> & other)`: Appends the items of the `other` list to this list and returns a reference to this list.
- `QList<T> & QList::operator<<(const T & value)`: This is an overloaded function. Appends `value` to the list.
- `QList<T> & QList::operator=(const QList<T> & other)`: Assigns `other` to this list and returns a reference to this list.
- `bool QList::operator==(const QList<T> & other) const`: Returns `true` if `other` is equal to this list; otherwise returns `false`. Two lists are considered equal if they contain the same values in the same order. This function requires the value type to have an implementation of `operator==()`.
- `T & QList::operator[](int i)`: Returns the item at index position `i` as a modifiable reference. `i` must be a valid index position in the list (i.e., `0 <= i < size()`). This function is very fast (constant time).
- `const T & QList::operator[](int i) const`: This is an overloaded function. Same as `at()`.

### Related Non-Members

- `QDataStream & operator<<(QDataStream & out, const QList<T> & list)`: Writes the `list` to stream `out`. This function requires the value type to implement `operator<<()`.
- `QDataStream & operator>>(QDataStream & in, QList<T> & list)`: Reads a list from stream `in` into `list`. This function requires the value type to implement `operator>>()`.