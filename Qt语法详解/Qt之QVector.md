---
title: Qt之QVector
date: 2019-01-30 20:04:17
categories: Qt语法详解
---
&emsp;&emsp;The `QVector` class is a template class that provides a dynamic array.

Header    | Inherited By
----------|-------------
`QVector` | `Q3ValueVector`, `QPolygon`, `QPolygonF`, `QStack`, and `QXmlStreamAttributes`

**Note**: All functions in this class are reentrant.

### Public Functions

Return            | Function
------------------|---------
                  | `QVector()`
                  | `QVector(int size)`
                  | `QVector(int size, const T & value)`
                  | `QVector(const QVector<T> & other)`
                  | `QVector(std::initializer_list<T> args)`
                  | `~QVector()`
`void`            | `append(const T & value)`
`const T &`       | `at(int i) const`
`reference`       | `back()`
`const_reference` | `back() const`
`iterator`        | `begin()`
`const_iterator`  | `begin() const`
`int`             | `capacity() const`
`void`            | `clear()`
`const_iterator`  | `constBegin() const`
`const T *`       | `constData() const`
`const_iterator`  | `constEnd() const`
`bool`            | `contains(const T & value) const`
`int`             | `count(const T & value) const`
`int`             | `count() const`
`T *`             | `data()`
`const T *`       | `data() const`
`bool`            | `empty() const`
`iterator`        | `end()`
`const_iterator`  | `end() const`
`bool`            | `endsWith(const T & value) const`
`iterator`        | `erase(iterator pos)`
`iterator`        | `erase(iterator begin, iterator end)`
`QVector<T> &`    | `fill(const T & value, int size = -1)`
`T &`             | `first()`
`const T &`       | `first() const`
`T &`             | `front()`
`const_reference` | `front() const`
`int`             | `indexOf(const T & value, int from = 0) const`
`void`            | `insert(int i, const T & value)`
`iterator`        | `insert(iterator before, int count, const T & value)`
`void`            | `insert(int i, int count, const T & value)`
`iterator`        | `insert(iterator before, const T & value)`
`bool`            | `isEmpty() const`
`T &`             | `last()`
`const T &`       | `last() const`
`int`             | `lastIndexOf(const T & value, int from = -1) const`
`QVector<T>`      | `mid(int pos, int length = -1) const`
`void`            | `pop_back()`
`void`            | `pop_front()`
`void`            | `prepend(const T & value)`
`void`            | `push_back(const T & value)`
`void`            | `push_front(const T & value)`
`void`            | `remove(int i)`
`void`            | `remove(int i, int count)`
`void`            | `replace(int i, const T & value)`
`void`            | `reserve(int size)`
`void`            | `resize(int size)`
`int`             | `size() const`
`void`            | `squeeze()`
`bool`            | `startsWith(const T & value) const`
`void`            | `swap(QVector<T> & other)`
`QList<T>`        | `toList() const`
`std::vector<T>`  | `toStdVector() const`
`T`               | `value(int i) const`
`T`               | `value(int i, const T & defaultValue) const`
`bool`            | `operator!=(const QVector<T> & other) const`
`QVector<T>`      | `operator+(const QVector<T> & other) const`
`QVector<T> &`    | `operator+=(const QVector<T> & other)`
`QVector<T> &`    | `operator+=(const T & value)`
`QVector<T> &`    | `operator<<(const T & value)`
`QVector<T> &`    | `operator<<(const QVector<T> & other)`
`QVector<T> &`    | `operator=(const QVector<T> & other)`
`QVector<T>`      | `operator=(QVector<T> && other)`
`bool`            | `operator==(const QVector<T> & other) const`
`T &`             | `operator[](int i)`
`const T &`       | `operator[](int i) const`

### Static Public Members

Return       | Function
-------------|---------
`QVector<T>` | `fromList(const QList<T> & list)`
`QVector<T>` | `fromStdVector(const std::vector<T> & vector)`

### Related Non-Members

Return          | Function
----------------|---------
`QDataStream &` | `operator<<(QDataStream & out, const QVector<T> & vector)`
`QDataStream &` | `operator>>(QDataStream & in, QVector<T> & vector)`

### Detailed Description

&emsp;&emsp;The `QVector` class is a template class that provides a dynamic array.
&emsp;&emsp;`QVector<T>` is one of Qt's generic container classes. It stores its items in adjacent memory locations and provides fast `index-based` access.
&emsp;&emsp;`QList<T>`, `QLinkedList<T>`, and `QVarLengthArray<T>` provide similar functionality. Here's an overview:

- For most purposes, `QList` is the right class to use. Operations like `prepend()` and `insert()` are usually faster than with `QVector` because of the way `QList` stores its items in memory, and its `index-based` `API` is more convenient than `QLinkedList's` `iterator-based` `API`. It also expands to less code in your executable.
- If you need a real linked list, with guarantees of constant time insertions in the middle of the list and iterators to items rather than indexes, use `QLinkedList`.
- If you want the items to occupy adjacent memory positions, or if your items are larger than a pointer and you want to avoid the overhead of allocating them on the heap individually at insertion time, then use `QVector`.
- If you want a `low-level` `variable-size` array, `QVarLengthArray` may be sufficient.

&emsp;&emsp;Here's an example of a `QVector` that stores integers and a `QVector` that stores `QString` values:

``` cpp
QVector<int> integerVector;
QVector<QString> stringVector;
```

`QVector` stores a vector (or array) of items. Typically, vectors are created with an initial size. For example, the following code constructs a `QVector` with `200` elements:

``` cpp
QVector<QString> vector ( 200 );
```

The elements are automatically initialized with a `default-constructed` value. If you want to initialize the vector with a different value, pass that value as the second argument to the constructor:

``` cpp
QVector<QString> vector ( 200, "Pass" );
```

You can also call `fill()` at any time to fill the vector with a value.
&emsp;&emsp;`QVector` uses `0-based` indexes, just like `C++` arrays. To access the item at a particular index position, you can use `operator[]()`. On `non-const` vectors, `operator[]()` returns a reference to the item that can be used on the left side of an assignment:

``` cpp
if ( vector[0] == "Liz" ) {
    vector[0] = "Elizabeth";
}
```

For `read-only` access, an alternative syntax is to use `at()`:

``` cpp
for ( int i = 0; i < vector.size(); ++i ) {
    if ( vector.at ( i ) == "Alfonso" ) {
        cout << "Found Alfonso at position " << i << endl;
    }
}
```

`at()` can be faster than `operator[]()`, because it never causes a deep copy to occur.
&emsp;&emsp;Another way to access the data stored in a `QVector` is to call `data()`. The function returns a pointer to the first item in the vector. You can use the pointer to directly access and modify the elements stored in the vector. The pointer is also useful if you need to pass a `QVector` to a function that accepts a plain `C++` array.
&emsp;&emsp;If you want to find all occurrences of a particular value in a vector, use `indexOf()` or `lastIndexOf()`. The former searches forward starting from a given index position, the latter searches backward. Both return the index of the matching item if they found one; otherwise, they return `-1`. For example:

``` cpp
int i = vector.indexOf ( "Harumi" );
​
if ( i != -1 ) {
    cout << "First occurrence of Harumi is at position " << i << endl;
}
```

&emsp;&emsp;If you simply want to check whether a vector contains a particular value, use `contains()`. If you want to find out how many times a particular value occurs in the vector, use `count()`.
&emsp;&emsp;`QVector` provides these basic functions to add, move, and remove items: `insert()`, `replace()`, `remove()`, `prepend()`, `append()`. With the exception of `append()` and `replace()`, these functions can be slow (linear time) for large vectors, because they require moving many items in the vector by one position in memory. If you want a container class that provides fast insertion/removal in the middle, use `QList` or `QLinkedList` instead.
&emsp;&emsp;Unlike plain `C++` arrays, `QVectors` can be resized at any time by calling `resize()`. If the new size is larger than the old size, `QVector` might need to reallocate the whole vector. `QVector` tries to reduce the number of reallocations by preallocating up to twice as much memory as the actual data needs.
&emsp;&emsp;If you know in advance approximately how many items the `QVector` will contain, you can call `reserve()`, asking `QVector` to preallocate a certain amount of memory. You can also call `capacity()` to find out how much memory `QVector` actually allocated.
&emsp;&emsp;Note that using `non-const` operators and functions can cause `QVector` to do a deep copy of the data. This is due to implicit sharing.
&emsp;&emsp;`QVector's` value type must be an assignable data type. This covers most data types that are commonly used, but the compiler won't let you, for example, store a `QWidget` as a value; instead, store a `QWidget *`. A few functions have additional requirements; for example, `indexOf()` and `lastIndexOf()` expect the value type to support `operator==()`. These requirements are documented on a `per-function` basis.
&emsp;&emsp;Like the other container classes, `QVector` provides `Java-style` iterators (`QVectorIterator` and `QMutableVectorIterator`) and `STL-style` iterators (`QVector::const_iterator` and `QVector::iterator`). In practice, these are rarely used, because you can use indexes into the `QVector`.
&emsp;&emsp;In addition to `QVector`, Qt also provides `QVarLengthArray`, a very `low-level` class with little functionality that is optimized for speed.
&emsp;&emsp;`QVector` does not support inserting, prepending, appending or replacing with references to its own values. Doing so will cause your application to abort with an error message.

### Member Type Documentation

- typedef `QVector::ConstIterator`: `Qt-style` synonym for `QVector::const_iterator`.
- typedef `QVector::Iterator`: `Qt-style` synonym for `QVector::iterator`.
- typedef `QVector::const_iterator`: The `QVector::const_iterator` typedef provides an `STL-style` const iterator for `QVector` and `QStack`. `QVector` provides both `STL-style` iterators and `Java-style` iterators. The `STL-style` const iterator is simply a typedef for `const T *` (pointer to `const T`).
- typedef `QVector::const_pointer`: Typedef for `const T *`. Provided for `STL` compatibility.
- typedef `QVector::const_reference`: Typedef for `T &`. Provided for `STL` compatibility.
- typedef `QVector::difference_type`: Typedef for `ptrdiff_t`. Provided for `STL` compatibility.
- typedef `QVector::iterator`: The `QVector::iterator` typedef provides an `STL-style` `non-const` iterator for `QVector` and `QStack`. `QVector` provides both `STL-style` iterators and `Java-style` iterators. The `STL-style` `non-const` iterator is simply a typedef for `T *` (pointer to `T`).
- typedef `QVector::pointer`: Typedef for `T *`. Provided for `STL` compatibility.
- typedef `QVector::reference`: Typedef for `T &`. Provided for `STL` compatibility.
- typedef `QVector::size_type`: Typedef for `int`. Provided for `STL` compatibility.
- typedef `QVector::value_type`: Typedef for `T`. Provided for `STL` compatibility.

### Member Function Documentation

- `QVector::QVector()`: Constructs an empty vector.
- `QVector::QVector(int size)`: Constructs a vector with an initial size of `size` elements. The elements are initialized with a `default-constructed` value.
- `QVector::QVector(int size, const T & value)`: Constructs a vector with an initial size of `size` elements. Each element is initialized with `value`.
- `QVector::QVector(const QVector<T> & other)`: Constructs a copy of `other`. This operation takes constant time, because `QVector` is implicitly shared. This makes returning a `QVector` from a function very fast. If a shared instance is modified, it will be copied (`copy-on-write`), and that takes linear time.
- `QVector::QVector(std::initializer_list<T> args)`: Construct a vector from the `std::initilizer_list` given by `args`. This constructor is only enabled if the compiler supports `C++0x`.
- `QVector::~QVector()`: Destroys the vector.
- `void QVector::append(const T & value)`: Inserts `value` at the end of the vector. Example:

``` cpp
QVector<QString> vector ( 0 );
vector.append ( "one" );
vector.append ( "two" );
vector.append ( "three" ); /* vector: ["one", "two", "three"] */
```

This is the same as calling `resize(size() + 1)` and assigning value to the new last element in the vector. This operation is relatively fast, because `QVector` typically allocates more memory than necessary, so it can grow without reallocating the entire vector each time.

- `const T & QVector::at(int i) const`: Returns the item at index position `i` in the vector. `i` must be a valid index position in the vector (i.e., `0 <= i < size()`).
- `reference QVector::back()`: This function is provided for `STL` compatibility. It is equivalent to `last()`.
- `const_reference QVector::back() const`: This is an overloaded function.
- `iterator QVector::begin()`: Returns an `STL-style` iterator pointing to the first item in the vector.
- `const_iterator QVector::begin() const`: This is an overloaded function.
- `int QVector::capacity() const`: Returns the maximum number of items that can be stored in the vector without forcing a reallocation. The sole purpose of this function is to provide a means of fine tuning `QVector's` memory usage. In general, you will rarely ever need to call this function. If you want to know how many items are in the vector, call `size()`.
- `void QVector::clear()`: Removes all the elements from the vector and releases the memory used by the vector.
- `const_iterator QVector::constBegin() const`: Returns a const `STL-style` iterator pointing to the first item in the vector.
- `const T * QVector::constData() const`: Returns a const pointer to the data stored in the vector. The pointer can be used to access the items in the vector. The pointer remains valid as long as the vector isn't reallocated. This function is mostly useful to pass a vector to a function that accepts a plain `C++` array.
- `const_iterator QVector::constEnd() const`: Returns a const `STL-style` iterator pointing to the imaginary item after the last item in the vector.
- `bool QVector::contains(const T & value) const`: Returns `true` if the vector contains an occurrence of `value`; otherwise returns `false`. This function requires the `value` type to have an implementation of `operator==()`.
- `int QVector::count(const T & value) const`: Returns the number of occurrences of `value` in the vector. This function requires the value type to have an implementation of `operator==()`.
- `int QVector::count() const`: This is an overloaded function. Same as `size()`.
- `T * QVector::data()`: Returns a pointer to the data stored in the vector. The pointer can be used to access and modify the items in the vector. Example:

``` cpp
QVector<int> vector ( 10 );
int *data = vector.data();
​
for ( int i = 0; i < 10; ++i ) {
    data[i] = 2 * i;
}
```

The pointer remains valid as long as the vector isn't reallocated. This function is mostly useful to pass a vector to a function that accepts a plain `C++` array.

- `const T * QVector::data() const`: This is an overloaded function.
- `bool QVector::empty() const`: This function is provided for `STL` compatibility. It is equivalent to `isEmpty()`, returning `true` if the vector is empty; otherwise returns `false`.
- `iterator QVector::end()`: Returns an `STL-style` iterator pointing to the imaginary item after the last item in the vector.
- `const_iterator QVector::end() const`: This is an overloaded function.
- `bool QVector::endsWith(const T & value) const`: Returns `true` if this vector is not empty and its last item is equal to `value`; otherwise returns `false`.
- `iterator QVector::erase(iterator pos)`: Removes the item pointed to by the iterator `pos` from the vector, and returns an iterator to the next item in the vector (which may be `end()`).
- `iterator QVector::erase(iterator begin, iterator end)`: This is an overloaded function. Removes all the items from `begin` up to (but not including) `end`. Returns an iterator to the same item that end referred to before the call.
- `QVector<T> & QVector::fill(const T & value, int size = -1)`: Assigns `value` to all items in the vector. If `size` is different from `-1` (the default), the vector is resized to `size` beforehand. Example:

``` cpp
QVector<QString> vector ( 3 );
vector.fill ( "Yes" ); /* vector: ["Yes", "Yes", "Yes"] */
vector.fill ( "oh", 5 ); /* vector: ["oh", "oh", "oh", "oh", "oh"] */
```

- `T & QVector::first()`: Returns a reference to the first item in the vector. This function assumes that the vector isn't empty.
- `const T & QVector::first() const`: This is an overloaded function.
- `QVector<T> QVector::fromList(const QList<T> & list) [static]`: Returns a `QVector` object with the data contained in `list`. Example:

``` cpp
QStringList list;
list << "Sven" << "Kim" << "Ola";
QVector<QString> vect = QVector<QString>::fromList ( list ); /* vect: ["Sven", "Kim", "Ola"] */
```

- `QVector<T> QVector::fromStdVector(const std::vector<T> & vector) [static]`: Returns a `QVector` object with the data contained in `vector`. The order of the elements in the `QVector` is the same as in vector. Example:

``` cpp
std::vector<double> stdvector;
vector.push_back ( 1.2 );
vector.push_back ( 0.5 );
vector.push_back ( 3.14 );
QVector<double> vector = QVector<double>::fromStdVector ( stdvector );
```

- `T & QVector::front()`: This function is provided for `STL` compatibility. It is equivalent to `first()`.
- `const_reference QVector::front() const`: This is an overloaded function.
- `int QVector::indexOf(const T & value, int from = 0) const`: Returns the index position of the first occurrence of `value` in the vector, searching forward `from` index position `from`. Returns `-1` if no item matched. Example:

``` cpp
QVector<QString> vector;
vector << "A" << "B" << "C" << "B" << "A";
vector.indexOf ( "B" );    // returns 1
vector.indexOf ( "B", 1 ); // returns 1
vector.indexOf ( "B", 2 ); // returns 3
vector.indexOf ( "X" );    // returns -1
```

This function requires the value type to have an implementation of `operator==()`.

- `void QVector::insert(int i, const T & value)`: Inserts `value` at index position `i` in the vector. If `i` is `0`, the `value` is prepended to the vector. If `i` is `size()`, the `value` is appended to the vector. Example:

``` cpp
QVector<QString> vector;
vector << "alpha" << "beta" << "delta";
vector.insert ( 2, "gamma" ); /* vector: ["alpha", "beta", "gamma", "delta"] */
```

For large vectors, this operation can be slow (linear time), because it requires moving all the items at indexes i and above by one position further in memory. If you want a container class that provides a fast `insert()` function, use `QLinkedList` instead.

- `iterator QVector::insert(iterator before, int count, const T & value)`: Inserts `count` copies of `value` in front of the item pointed to by the iterator `before`. Returns an iterator pointing at the first of the inserted items.
- `void QVector::insert(int i, int count, const T & value)`: This is an overloaded function. Inserts `count` copies of `value` at index position `i` in the vector. Example:

``` cpp
QVector<double> vector;
vector << 2.718 << 1.442 << 0.4342;
vector.insert ( 1, 3, 9.9 ); /* vector: [2.718, 9.9, 9.9, 9.9, 1.442, 0.4342] */
```

- `iterator QVector::insert(iterator before, const T & value)`: This is an overloaded function. Inserts `value` in front of the item pointed to by the iterator `before`. Returns an iterator pointing at the inserted item.
- `bool QVector::isEmpty() const`: Returns `true` if the vector has size `0`; otherwise returns `false`.
- `T & QVector::last()`: Returns a reference to the last item in the vector. This function assumes that the vector isn't empty.
- `const T & QVector::last() const`: This is an overloaded function.
- `int QVector::lastIndexOf(const T & value, int from = -1) const`: Returns the index position of the last occurrence of the `value` in the vector, searching backward from index position `from`. If `from` is `-1` (the default), the search starts at the last item. Returns `-1` if no item matched. Example:

``` cpp
QList<QString> vector;
vector << "A" << "B" << "C" << "B" << "A";
vector.lastIndexOf ( "B" );    // returns 3
vector.lastIndexOf ( "B", 3 ); // returns 3
vector.lastIndexOf ( "B", 2 ); // returns 1
vector.lastIndexOf ( "X" );    // returns -1
```

This function requires the value type to have an implementation of `operator==()`.

- `QVector<T> QVector::mid(int pos, int length = -1) const`: Returns a vector whose elements are copied from this vector, starting at position `pos`. If `length` is `-1` (the default), all elements after `pos` are copied; otherwise `length` elements (or all remaining elements if there are less than `length` elements) are copied.
- `void QVector::pop_back()`: This function is provided for `STL` compatibility. It is equivalent to `erase(end() - 1)`.
- `void QVector::pop_front()`: This function is provided for `STL` compatibility. It is equivalent to `erase(begin())`.
- `void QVector::prepend(const T & value)`: Inserts `value` at the beginning of the vector. Example:

``` cpp
QVector<QString> vector;
vector.prepend ( "one" );
vector.prepend ( "two" );
vector.prepend ( "three" ); /* vector: ["three", "two", "one"] */
```

This is the same as `vector.insert(0, value)`. For large vectors, this operation can be slow (linear time), because it requires moving all the items in the vector by one position further in memory. If you want a container class that provides a fast `prepend()` function, use `QList` or `QLinkedList` instead.

- `void QVector::push_back(const T & value)`: This function is provided for `STL` compatibility. It is equivalent to `append(value)`.
- `void QVector::push_front(const T & value)`: This function is provided for `STL` compatibility. It is equivalent to `prepend(value)`.
- `void QVector::remove(int i)`: This is an overloaded function. Removes the element at index position `i`.
- `void QVector::remove(int i, int count)`: This is an overloaded function. Removes `count` elements from the middle of the vector, starting at index position `i`.
- `void QVector::replace(int i, const T & value)`: Replaces the item at index position `i` with `value`. `i` must be a valid index position in the vector (i.e., `0 <= i < size()`).
- `void QVector::reserve(int size)`: Attempts to allocate memory for at least `size` elements. If you know in advance how large the vector will be, you can call this function, and if you call `resize()` often you are likely to get better performance. If size is an underestimate, the worst that will happen is that the `QVector` will be a bit slower. The sole purpose of this function is to provide a means of fine tuning `QVector's` memory usage. In general, you will rarely ever need to call this function. If you want to change the size of the vector, call `resize()`.
- `void QVector::resize(int size)`: Sets the size of the vector to `size`. If `size` is greater than the current size, elements are added to the end; the new elements are initialized with a `default-constructed` value. If `size` is less than the current size, elements are removed from the end.
- `int QVector::size() const`: Returns the number of items in the vector.
- `void QVector::squeeze()`: Releases any memory not required to store the items. The sole purpose of this function is to provide a means of fine tuning `QVector's` memory usage. In general, you will rarely ever need to call this function.
- `bool QVector::startsWith(const T & value) const`: Returns `true` if this vector is not empty and its first item is equal to `value`; otherwise returns `false`.
- `void QVector::swap(QVector<T> & other)`: Swaps vector `other` with this vector. This operation is very fast and never fails.
- `QList<T> QVector::toList() const`: Returns a `QList` object with the data contained in this `QVector`. Example:

``` cpp
QVector<QString> vect;
vect << "red" << "green" << "blue" << "black";
QList<QString> list = vect.toList(); /* list: ["red", "green", "blue", "black"] */
```

- `std::vector<T> QVector::toStdVector() const`: Returns a `std::vector` object with the data contained in this `QVector`. Example:

``` cpp
QVector<double> vector;
vector << 1.2 << 0.5 << 3.14;
std::vector<double> stdvector = vector.toStdVector();
```

- `T QVector::value(int i) const`: Returns the value at index position `i` in the vector. If the index `i` is out of bounds, the function returns a `default-constructed` value. If you are certain that `i` is within bounds, you can use `at()` instead, which is slightly faster.
- `T QVector::value(int i, const T & defaultValue) const`: This is an overloaded function. If the index `i` is out of bounds, the function returns `defaultValue`.
- `bool QVector::operator!=(const QVector<T> & other) const`: Returns `true` if `other` is not equal to this vector; otherwise returns `false`. Two vectors are considered equal if they contain the same values in the same order. This function requires the value type to have an implementation of `operator==()`.
- `QVector<T> QVector::operator+(const QVector<T> & other) const`: Returns a vector that contains all the items in this vector followed by all the items in the `other` vector.
- `QVector<T> & QVector::operator+=(const QVector<T> & other)`: Appends the items of the `other` vector to this vector and returns a reference to this vector.
- `QVector<T> & QVector::operator+=(const T & value)`: This is an overloaded function. Appends `value` to the vector.
- `QVector<T> & QVector::operator<<(const T & value)`: Appends `value` to the vector and returns a reference to this vector.
- `QVector<T> & QVector::operator<<(const QVector<T> & other)`: Appends `other` to the vector and returns a reference to the vector.
- `QVector<T> & QVector::operator=(const QVector<T> & other)`: Assigns `other` to this vector and returns a reference to this vector.
- `bool QVector::operator==(const QVector<T> & other) const`: Returns `true` if `other` is equal to this vector; otherwise returns `false`. Two vectors are considered equal if they contain the same values in the same order. This function requires the value type to have an implementation of `operator==()`.
- `T & QVector::operator[](int i)`: Returns the item at index position `i` as a modifiable reference. `i` must be a valid index position in the vector (i.e., `0 <= i < size()`). Note that using `non-const` operators can cause `QVector` to do a deep copy.
- `const T & QVector::operator[](int i) const`: This is an overloaded function. Same as `at(i)`.

### Related Non-Members

- `QDataStream & operator<<(QDataStream & out, const QVector<T> & vector)`: Writes the `vector` to stream `out`. This function requires the value type to implement `operator<<()`.
- `QDataStream & operator>>(QDataStream & in, QVector<T> & vector)`: Reads a vector from stream `in` into `vector`. This function requires the value type to implement `operator>>()`.