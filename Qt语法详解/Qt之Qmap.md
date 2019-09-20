---
title: Qt之Qmap
date: 2019-01-28 10:46:17
categories: Qt语法详解
---
&emsp;&emsp;The `QMap` class is a template class that provides a `skip-list-based` dictionary.

Header | Inherited By
-------|---------------
`QMap` | `QMultiMap`

**Note**: All functions in this class are reentrant.

### Public Functions

Return             | Function
-------------------|----------
                   | `QMap()`
                   | `QMap(const QMap<Key, T> & other)`
                   | `QMap(const std::map<Key, T> & other)`
                   | `~QMap()`
`iterator`         | `begin()`
`const_iterator`   | `begin() const`
`void`             | `clear()`
`const_iterator`   | `constBegin() const`
`const_iterator`   | `constEnd() const`
`const_iterator`   | `constFind(const Key & key) const`
`bool`             | `contains(const Key & key) const`
`int`              | `count(const Key & key) const`
`int`              | `count() const`
`bool`             | `empty() const`
`iterator`         | `end()`
`const_iterator`   | `end() const`
`iterator`         | `erase(iterator pos)`
`iterator`         | `find(const Key & key)`
`const_iterator`   | `find(const Key & key) const`
`iterator`         | `insert(const Key & key, const T & value)`
`iterator`         | `insertMulti(const Key & key, const T & value)`
`bool`             | `isEmpty() const`
`const Key`        | `key(const T & value) const`
`const Key`        | `key(const T & value, const Key & defaultKey) const`
`QList<Key>`       | `keys() const`
`QList<Key>`       | `keys(const T & value) const`
`iterator`         | `lowerBound(const Key & key)`
`const_iterator`   | `lowerBound(const Key & key) const`
`int`              | `remove(const Key & key)`
`int`              | `size() const`
`void`             | `swap(QMap<Key, T> & other)`
`T`                | `take(const Key & key)`
`std::map<Key, T>` | `toStdMap() const`
`QList<Key>`       | `uniqueKeys() const`
`QMap<Key, T> &`   | `unite(const QMap<Key, T> & other)`
`iterator`         | `upperBound(const Key & key)`
`const_iterator`   | `upperBound(const Key & key) const`
`const T`          | `value(const Key & key) const`
`const T`          | `value(const Key & key, const T & defaultValue) const`
`QList<T>`         | `values() const`
`QList<T>`         | `values(const Key & key) const`
`bool`             | `operator!=(const QMap<Key, T> & other) const`
`QMap<Key, T> &`   | `operator=(const QMap<Key, T> & other)`
`QMap<Key, T> &`   | `operator=(QMap<Key, T> && other)`
`bool`             | `operator==(const QMap<Key, T> & other) const`
`T &`              | `operator[](const Key & key)`
`const T`          | `operator[](const Key & key) const`

### Related Non-Members

Return        | Function
--------------|---------
`QDataStream` | `&operator<< ( QDataStream &out, const QMap<Key, T> &map );`
`QDataStream` | `&operator>> ( QDataStream &in, QMap<Key, T> &map );`

### Detailed Description

&emsp;&emsp;The `QMap` class is a template class that provides a `skip-list-based` dictionary.
&emsp;&emsp;`QMap<Key, T>` is one of `Qt's` generic container classes. It stores `(key, value)` pairs and provides fast lookup of the value associated with a key.
&emsp;&emsp;`QMap` and `QHash` provide very similar functionality. The differences are:

- `QHash` provides faster lookups than `QMap`.
- When iterating over a `QHash`, the items are arbitrarily ordered. With `QMap`, the items are always sorted by key.
- The key type of a `QHash` must provide `operator==()` and a global `qHash(Key)` function. The key type of a `QMap` must provide `operator<()` specifying a total order.

&emsp;&emsp;Here's an example `QMap` with `QString` keys and int values:

``` cpp
QMap<QString, int> map;
```

To insert a `(key, value)` pair into the map, you can use `operator[]()`:

``` cpp
map["one"] = 1;
map["three"] = 3;
map["seven"] = 7;
```

&emsp;&emsp;This inserts the following three `(key, value)` pairs into the `QMap`: `("one", 1)`, `("three", 3)`, and `("seven", 7)`. Another way to insert items into the map is to use `insert()`:

``` cpp
map.insert ( "twelve", 12 );
```

&emsp;&emsp;To look up a value, use `operator[]()` or `value()`:

``` cpp
int num1 = map["thirteen"];
int num2 = map.value ( "thirteen" );
```

&emsp;&emsp;If there is no item with the specified key in the map, these functions return a `default-constructed` value.
&emsp;&emsp;If you want to check whether the map contains a certain key, use `contains()`:

``` cpp
int timeout = 30;
​
if ( map.contains ( "TIMEOUT" ) ) {
    timeout = map.value ( "TIMEOUT" );
}
```

&emsp;&emsp;There is also a `value()` overload that uses its second argument as a default value if there is no item with the specified key:

``` cpp
int timeout = map.value ( "TIMEOUT", 30 );
```

&emsp;&emsp;In general, we recommend that you use `contains()` and `value()` rather than `operator[]()` for looking up a key in a map. The reason is that `operator[]()` silently inserts an item into the map if no item exists with the same key (unless the map is const). For example, the following code snippet will create `1000` items in memory:

``` cpp
/* WRONG */
QMap<int, QWidget *> map;
​
for ( int i = 0; i < 1000; ++i ) {
    if ( map[i] == okButton ) {
        cout << "Found button at index " << i << endl;
    }
}
```

&emsp;&emsp;To avoid this problem, replace `map[i]` with `map.value(i)` in the code above.
&emsp;&emsp;If you want to navigate through all the `(key, value)` pairs stored in a `QMap`, you can use an iterator. `QMap` provides both `Java-style` iterators (`QMapIterator` and `QMutableMapIterator`) and `STL-style` iterators (`QMap::const_iterator` and `QMap::iterator`). Here's how to iterate over a `QMap<QString, int>` using a `Java-style` iterator:

``` cpp
QMapIterator<QString, int> i ( map );
​
while ( i.hasNext() ) {
    i.next();
    cout << i.key() << ": " << i.value() << endl;
}
```

&emsp;&emsp;Here's the same code, but using an `STL-style` iterator this time:

``` cpp
QMap<QString, int>::const_iterator i = map.constBegin();
​
while ( i != map.constEnd() ) {
    cout << i.key() << ": " << i.value() << endl;
    ++i;
}
```

The items are traversed in ascending key order.
&emsp;&emsp;Normally, a `QMap` allows only one value per key. If you call `insert()` with a key that already exists in the `QMap`, the previous value will be erased. For example:

``` cpp
map.insert ( "plenty", 100 );
map.insert ( "plenty", 2000 ); /* map.value("plenty") == 2000 */
```

&emsp;&emsp;However, you can store multiple values per key by using `insertMulti()` instead of `insert()` (or using the convenience subclass `QMultiMap`). If you want to retrieve all the values for a single key, you can use `values(const Key &key)`, which returns a `QList<T>`:

``` cpp
QList<int> values = map.values ( "plenty" );
​
for ( int i = 0; i < values.size(); ++i ) {
    cout << values.at ( i ) << endl;
}
```

&emsp;&emsp;The items that share the same key are available from most recently to least recently inserted. Another approach is to call `find()` to get the `STL-style` iterator for the first item with a key and iterate from there:

``` cpp
QMap<QString, int>::iterator i = map.find ( "plenty" );
​
while ( i != map.end() && i.key() == "plenty" ) {
    cout << i.value() << endl;
    ++i;
}
```

&emsp;&emsp;If you only need to extract the values from a map (not the keys), you can also use `foreach`:

``` cpp
QMap<QString, int> map;
​
foreach ( int value, map ) {
    cout << value << endl;
}
```

&emsp;&emsp;Items can be removed from the map in several ways. One way is to call `remove()`; this will remove any item with the given key. Another way is to use `QMutableMapIterator::remove()`. In addition, you can clear the entire map using `clear()`.
&emsp;&emsp;`QMap's` key and value data types must be assignable data types. This covers most data types you are likely to encounter, but the compiler won't let you, for example, store a `QWidget` as a value; instead, store a `QWidget *`. In addition, `QMap's` key type must provide `operator<()`. `QMap` uses it to keep its items sorted, and assumes that two keys `x` and `y` are equal if neither `x < y` nor `y < x` is `true`. Example:

``` cpp
#ifndef EMPLOYEE_H
#define EMPLOYEE_H
​
class Employee {
public:
    Employee() {}
    Employee ( const QString &name, const QDate &dateOfBirth );
private:
    QString myName;
    QDate myDateOfBirth;
};
​
inline bool operator< ( const Employee &e1, const Employee &e2 ) {
    if ( e1.name() != e2.name() ) {
        return e1.name() < e2.name();
    }
​
    return e1.dateOfBirth() < e2.dateOfBirth();
}
​
#endif // EMPLOYEE_H
```

&emsp;&emsp;In the example, we start by comparing the employees' names. If they're equal, we compare their dates of birth to break the tie.

### Member Type Documentation

- typedef `QMap::ConstIterator`: `Qt-style` synonym for `QMap::const_iterator`.
- typedef `QMap::Iterator`: `Qt-style` synonym for `QMap::iterator`.
- typedef `QMap::difference_type`: Typedef for `ptrdiff_t`. Provided for `STL` compatibility.
- typedef `QMap::key_type`: Typedef for `Key`. Provided for `STL` compatibility.
- typedef `QMap::mapped_type`: Typedef for `T`. Provided for `STL` compatibility.
- typedef `QMap::size_type`: Typedef for `int`. Provided for `STL` compatibility.

### Member Function Documentation

- `QMap::QMap()`: Constructs an empty map.
- `QMap::QMap(const QMap<Key, T> & other)`: Constructs a copy of `other`. This operation occurs in constant time, because `QMap` is implicitly shared. This makes returning a `QMap` from a function very fast. If a shared instance is modified, it will be copied (`copy-on-write`), and this takes linear time.
- `QMap::QMap(const std::map<Key, T> & other)`: Constructs a copy of `other`. This function is only available if `Qt` is configured with `STL` compatibility enabled.
- `QMap::~QMap()`: Destroys the map. References to the values in the map, and all iterators over this map, become invalid.
- `iterator QMap::begin()`: Returns an `STL-style` iterator pointing to the first item in the map.
- `const_iterator QMap::begin() const`: This is an overloaded function.
- `void QMap::clear()`: Removes all items from the map.
- `const_iterator QMap::constBegin() const`: Returns a const `STL-style` iterator pointing to the first item in the map.
- `const_iterator QMap::constEnd() const`: Returns a const `STL-style` iterator pointing to the imaginary item after the last item in the map.
- `const_iterator QMap::constFind(const Key & key) const`: Returns an const iterator pointing to the item with `key` in the map. If the map contains no item with `key`, the function returns `constEnd()`.
- `bool QMap::contains(const Key & key) const`: Returns `true` if the map contains an item with `key`; otherwise returns `false`.
- `int QMap::count(const Key & key) const`: Returns the number of items associated with `key`.
- `int QMap::count() const`: This is an overloaded function. Same as `size()`.
- `bool QMap::empty() const`: This function is provided for `STL` compatibility. It is equivalent to `isEmpty()`, returning `true` if the map is empty; otherwise returning `false`.
- `iterator QMap::end()`: Returns an `STL-style` iterator pointing to the imaginary item after the last item in the map.
- `const_iterator QMap::end() const`: This is an overloaded function.
- `iterator QMap::erase(iterator pos)`: Removes the `(key, value)` pair pointed to by the iterator `pos` from the map, and returns an iterator to the next item in the map.
- `iterator QMap::find(const Key & key)`: Returns an iterator pointing to the item with `key` in the map. If the map contains no item with `key`, the function returns `end()`. If the map contains multiple items with `key`, this function returns an iterator that points to the most recently inserted value. The other values are accessible by incrementing the iterator. For example, here's some code that iterates over all the items with the same `key`:

``` cpp
QMap<QString, int> map;
​
QMap<QString, int>::const_iterator i = map.find ( "HDR" );
​
while ( i != map.end() && i.key() == "HDR" ) {
    cout << i.value() << endl;
    ++i;
}
```

- `const_iterator QMap::find(const Key & key) const`: This is an overloaded function.
- `iterator QMap::insert(const Key & key, const T & value)`: Inserts a new item with the `key` and a `value` of value. If there is already an item with the `key`, that item's value is replaced with `value`. If there are multiple items with the `key`, the most recently inserted item's value is replaced with `value`.
- `iterator QMap::insertMulti(const Key & key, const T & value)`: Inserts a new item with the `key` and a `value` of value. If there is already an item with the same `key` in the map, this function will simply create a new one (This behavior is different from `insert()`, which overwrites the value of an existing item).
- `bool QMap::isEmpty() const`: Returns `true` if the map contains no items; otherwise returns `false`.
- `const Key QMap::key(const T & value) const`: Returns the first key with `value`. If the map contains no item with `value`, the function returns a `default-constructed` key. This function can be slow (linear time), because `QMap's` internal data structure is optimized for fast lookup by key, not by value.
- `const Key QMap::key(const T & value, const Key & defaultKey) const`: This is an overloaded function. Returns the first key with `value`, or `defaultKey` if the map contains no item with `value`. This function can be slow (linear time), because `QMap's` internal data structure is optimized for fast lookup by key, not by value.
- `QList<Key> QMap::keys() const`: Returns a list containing all the keys in the map in ascending order. Keys that occur multiple times in the map (because items were inserted with `insertMulti()`, or `unite()` was used) also occur multiple times in the list. To obtain a list of unique keys, where each key from the map only occurs once, use `uniqueKeys()`. The order is guaranteed to be the same as that used by `values()`.
- `QList<Key> QMap::keys(const T & value) const`: This is an overloaded function. Returns a list containing all the keys associated with `value` in ascending order. This function can be slow (linear time), because `QMap's` internal data structure is optimized for fast lookup by key, not by value.
- `iterator QMap::lowerBound(const Key & key)`: Returns an iterator pointing to the first item with `key` in the map. If the map contains no item with `key`, the function returns an iterator to the nearest item with a greater key. Example:

``` cpp
QMap<int, QString> map;
map.insert ( 1, "one" );
map.insert ( 5, "five" );
map.insert ( 10, "ten" );
​
map.lowerBound ( 0 ); /* returns iterator to (1, "one") */
map.lowerBound ( 1 ); /* returns iterator to (1, "one") */
map.lowerBound ( 2 ); /* returns iterator to (5, "five") */
map.lowerBound ( 10 ); /* returns iterator to (10, "ten") */
map.lowerBound ( 999 ); /* returns end() */
```

If the map contains multiple items with `key`, this function returns an iterator that points to the most recently inserted value. The other values are accessible by incrementing the iterator. For example, here's some code that iterates over all the items with the same key:

``` cpp
QMap<QString, int> map;
​
QMap<QString, int>::const_iterator i = map.lowerBound ( "HDR" );
QMap<QString, int>::const_iterator upperBound = map.upperBound ( "HDR" );
​
while ( i != upperBound ) {
    cout << i.value() << endl;
    ++i;
}
```

- `const_iterator QMap::lowerBound(const Key & key) const`: This is an overloaded function.
- `int QMap::remove(const Key & key)`: Removes all the items that have the `key` from the map. Returns the number of items removed which is usually `1` but will be `0` if the `key` isn't in the map, or `> 1` if `insertMulti()` has been used with the `key`.
- `int QMap::size() const`: Returns the number of `(key, value)` pairs in the map.
- `void QMap::swap(QMap<Key, T> & other)`: Swaps map `other` with this map. This operation is very fast and never fails.
- `T QMap::take(const Key & key)`: Removes the item with the `key` from the map and returns the value associated with it. If the item does not exist in the map, the function simply returns a `default-constructed` value. If there are multiple items for `key` in the map, only the most recently inserted one is removed and returned. If you don't use the return value, `remove()` is more efficient.
- `std::map<Key, T> QMap::toStdMap() const`: Returns an `STL` map equivalent to this `QMap`. This function is only available if `Qt` is configured with `STL` compatibility enabled.
- `QList<Key> QMap::uniqueKeys() const`: Returns a list containing all the keys in the map in ascending order. Keys that occur multiple times in the map (because items were inserted with `insertMulti()`, or `unite()` was used) occur only once in the returned list.
- `QMap<Key, T> & QMap::unite(const QMap<Key, T> & other)`: Inserts all the items in the `other` map into this map. If a key is common to both maps, the resulting map will contain the key multiple times.
- `iterator QMap::upperBound(const Key & key)`: Returns an iterator pointing to the item that immediately follows the last item with `key` in the map. If the map contains no item with `key`, the function returns an iterator to the nearest item with a greater key. Example:

``` cpp
QMap<int, QString> map;
map.insert ( 1, "one" );
map.insert ( 5, "five" );
map.insert ( 10, "ten" );
​
map.upperBound ( 0 ); /* returns iterator to (1, "one") */
map.upperBound ( 1 ); /* returns iterator to (5, "five") */
map.upperBound ( 2 ); /* returns iterator to (5, "five") */
map.upperBound ( 10 ); /* returns end() */
map.upperBound ( 999 ); /* returns end() */
```

- `const_iterator QMap::upperBound(const Key & key) const`: This is an overloaded function.
- `const T QMap::value(const Key & key) const`: Returns the value associated with the `key`. If the map contains no item with `key`, the function returns a `default-constructed` value. If there are multiple items for key in the map, the value of the most recently inserted one is returned.
- `const T QMap::value(const Key & key, const T & defaultValue) const`: This is an overloaded function. If the map contains no item with `key`, the function returns `defaultValue`.
- `QList<T> QMap::values() const`: Returns a list containing all the values in the map, in ascending order of their keys. If a key is associated with multiple values, all of its values will be in the list, and not just the most recently inserted one.
- `QList<T> QMap::values(const Key & key) const`: This is an overloaded function. Returns a list containing all the values associated with `key`, from the most recently inserted to the least recently inserted one.
- `bool QMap::operator!=(const QMap<Key, T> & other) const`: Returns true if `other` is not equal to this map; otherwise returns false. Two maps are considered equal if they contain the same `(key, value)` pairs. This function requires the value type to implement `operator==()`.
- `QMap<Key, T> & QMap::operator=(const QMap<Key, T> & other)`: Assigns `other` to this map and returns a reference to this map.
- `QMap<Key, T> & QMap::operator=(QMap<Key, T> && other)`: bool `QMap::operator==(const QMap<Key, T> & other)` const Returns `true` if `other` is equal to this map; otherwise returns `false`. Two maps are considered equal if they contain the same `(key, value)` pairs. This function requires the value type to implement `operator==()`.
- `T & QMap::operator[](const Key & key)`: Returns the value associated with the key `key` as a modifiable reference. If the map contains no item with `key`, the function inserts a `default-constructed` value into the map with `key`, and returns a reference to it. If the map contains multiple items with `key`, this function returns a reference to the most recently inserted value.
- `const T QMap::operator[](const Key & key) const`: This is an overloaded function.

### Related Non-Members

- `QDataStream & operator<<(QDataStream & out, const QMap<Key, T> & map)`: Writes the `map` to stream `out`. This function requires the key and value types to implement `operator<<()`.
- `QDataStream & operator>>(QDataStream & in, QMap<Key, T> & map)`: Reads a map from stream `in` into `map`. This function requires the key and value types to implement `operator>>()`.