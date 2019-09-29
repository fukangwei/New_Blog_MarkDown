---
title: Qt之QHash
categories: Qt语法详解
abbrlink: 8bae26cb
date: 2019-01-28 18:27:15
---
&emsp;&emsp;The `QHash` class is a template class that provides a `hash-table-based` dictionary.

Header  | Inherited By
--------|-------------
`QHash` | `QMultiHash`

**Note**: All functions in this class are reentrant.

### Public Functions

Return            | Function
------------------|---------
                  | `QHash()`
                  | `QHash(const QHash<Key, T> & other)`
                  | `~QHash()`
`iterator`        | `begin()`
`const_iterator`  | `begin() const`
`int`             | `capacity() const`
`void`            | `clear()`
`const_iterator`  | `constBegin() const`
`const_iterator`  | `constEnd() const`
`const_iterator`  | `constFind(const Key & key) const`
`bool`            | `contains(const Key & key) const`
`int`             | `count(const Key & key) const`
`int`             | `count() const`
`bool`            | `empty() const`
`iterator`        | `end()`
`const_iterator`  | `end() const`
`iterator`        | `erase(iterator pos)`
`iterator`        | `find(const Key & key)`
`const_iterator`  | `find(const Key & key) const`
`iterator`        | `insert(const Key & key, const T & value)`
`iterator`        | `insertMulti(const Key & key, const T & value)`
`bool`            | `isEmpty() const`
`const Key`       | `key(const T & value) const`
`const Key`       | `key(const T & value, const Key & defaultKey) const`
`QList<Key>`      | `keys() const`
`QList<Key>`      | `keys(const T & value) const`
`int`             | `remove(const Key & key)`
`void`            | `reserve(int size)`
`int`             | `size() const`
`void`            | `squeeze()`
`void`            | `swap(QHash<Key, T> & other)`
`T`               | `take(const Key & key)`
`QList<Key>`      | `uniqueKeys() const`
`QHash<Key, T> &` | `unite(const QHash<Key, T> & other)`
`const T`         | `value(const Key & key) const`
`const T`         | `value(const Key & key, const T & defaultValue) const`
`QList<T>`        | `values() const`
`QList<T>`        | `values(const Key & key) const`
`bool`            | `operator!=(const QHash<Key, T> & other) const`
`QHash<Key, T> &` | `operator=(const QHash<Key, T> & other)`
`QHash<Key, T> &` | `operator=(QHash<Key, T> && other)`
`bool`            | `operator==(const QHash<Key, T> & other) const`
`T &`             | `operator[](const Key & key)`
`const T`         | `operator[](const Key & key) const`

### Related Non-Members

Return          | Function
----------------|---------
`uint`          | `qHash(const QXmlNodeModelIndex & index)`
`uint`          | `qHash(char key)`
`uint`          | `qHash(uchar key)`
`uint`          | `qHash(signed char key)`
`uint`          | `qHash(ushort key)`
`uint`          | `qHash(short key)`
`uint`          | `qHash(uint key)`
`uint`          | `qHash(int key)`
`uint`          | `qHash(ulong key)`
`uint`          | `qHash(long key)`
`uint`          | `qHash(quint64 key)`
`uint`          | `qHash(qint64 key)`
`uint`          | `qHash(QChar key)`
`uint`          | `qHash(const QByteArray & key)`
`uint`          | `qHash(const QString & key)`
`uint`          | `qHash(const QBitArray & key)`
`uint`          | `qHash(const T * key)`
`uint`          | `qHash(const QPair<T1, T2> & key)`
`QDataStream &` | `operator<<(QDataStream & out, const QHash<Key, T> & hash)`
`QDataStream &` | `operator>>(QDataStream & in, QHash<Key, T> & hash)`

### Detailed Description

&emsp;&emsp;The `QHash` class is a template class that provides a `hash-table-based` dictionary.
&emsp;&emsp;`QHash<Key, T>` is one of `Qt's` generic container classes. It stores `(key, value)` pairs and provides very fast lookup of the value associated with a key.
&emsp;&emsp;`QHash` provides very similar functionality to `QMap`. The differences are:

- `QHash` provides faster lookups than `QMap`.
- When iterating over a `QMap`, the items are always sorted by key. With `QHash`, the items are arbitrarily ordered.
- The key type of a `QMap` must provide `operator<()`. The key type of a `QHash` must provide `operator==()` and a global hash function called `qHash()`.

&emsp;&emsp;Here's an example `QHash` with `QString` keys and int values:

``` cpp
QHash<QString, int> hash;
```

&emsp;&emsp;To insert a `(key, value)` pair into the hash, you can use `operator[]()`:

``` cpp
hash["one"] = 1;
hash["three"] = 3;
hash["seven"] = 7;
```

&emsp;&emsp;This inserts the following three `(key, value)` pairs into the `QHash`: `("one", 1)`, `("three", 3)`, and `("seven", 7)`. Another way to insert items into the hash is to use `insert()`:

``` cpp
hash.insert("twelve", 12);
```

&emsp;&emsp;To look up a value, use `operator[]()` or `value()`:

``` cpp
int num1 = hash["thirteen"];
int num2 = hash.value ( "thirteen" );
```

&emsp;&emsp;If there is no item with the specified key in the hash, these functions return a `default-constructed` value.
&emsp;&emsp;If you want to check whether the hash contains a particular key, use `contains()`:

``` cpp
int timeout = 30;
​
if ( hash.contains ( "TIMEOUT" ) ) {
    timeout = hash.value ( "TIMEOUT" );
}
```

&emsp;&emsp;There is also a `value()` overload that uses its second argument as a default value if there is no item with the specified key:

``` cpp
int timeout = hash.value ( "TIMEOUT", 30 );
```

&emsp;&emsp;In general, we recommend that you use `contains()` and `value()` rather than `operator[]()` for looking up a key in a hash. The reason is that `operator[]()` silently inserts an item into the hash if no item exists with the same key (unless the hash is `const`). For example, the following code snippet will create `1000` items in memory:

``` cpp
/* WRONG */
QHash<int, QWidget *> hash;
...
​
for ( int i = 0; i < 1000; ++i ) {
    if ( hash[i] == okButton ) {
        cout << "Found button at index " << i << endl;
    }
}
```

&emsp;&emsp;To avoid this problem, replace `hash[i]` with `hash.value(i)` in the code above.
&emsp;&emsp;If you want to navigate through all the `(key, value)` pairs stored in a `QHash`, you can use an iterator. `QHash` provides both `Java-style` iterators (`QHashIterator` and `QMutableHashIterator`) and `STL-style` iterators (`QHash::const_iterator` and `QHash::iterator`). Here's how to iterate over a `QHash<QString, int>` using a `Java-style` iterator:

``` cpp
QHashIterator<QString, int> i ( hash );
​
while ( i.hasNext() ) {
    i.next();
    cout << i.key() << ": " << i.value() << endl;
}
```

&emsp;&emsp;Here's the same code, but using an `STL-style` iterator:

``` cpp
QHash<QString, int>::const_iterator i = hash.constBegin();
​
while ( i != hash.constEnd() ) {
    cout << i.key() << ": " << i.value() << endl;
    ++i;
}
```

&emsp;&emsp;`QHash` is unordered, so an iterator's sequence cannot be assumed to be predictable. If ordering by key is required, use a `QMap`.
&emsp;&emsp;Normally, a `QHash` allows only one value per key. If you call `insert()` with a key that already exists in the `QHash`, the previous value is erased. For example:

``` cpp
hash.insert ( "plenty", 100 );
hash.insert ( "plenty", 2000 ); /* hash.value("plenty") == 2000 */
```

&emsp;&emsp;However, you can store multiple values per key by using `insertMulti()` instead of `insert()` (or using the convenience subclass `QMultiHash`). If you want to retrieve all the values for a single key, you can use `values(const Key &key)`, which returns a `QList<T>`:

``` cpp
QList<int> values = hash.values ( "plenty" );
​
for ( int i = 0; i < values.size(); ++i ) {
    cout << values.at ( i ) << endl;
}
```

&emsp;&emsp;The items that share the same key are available from most recently to least recently inserted. A more efficient approach is to call `find()` to get the iterator for the first item with a key and iterate from there:

``` cpp
QHash<QString, int>::iterator i = hash.find ( "plenty" );
​
while ( i != hash.end() && i.key() == "plenty" ) {
    cout << i.value() << endl;
    ++i;
}
```

&emsp;&emsp;If you only need to extract the values from a hash (not the keys), you can also use `foreach`:

``` cpp
QHash<QString, int> hash;
...
​
foreach ( int value, hash ) {
    cout << value << endl;
}
```

&emsp;&emsp;Items can be removed from the hash in several ways. One way is to call `remove()`; this will remove any item with the given key. Another way is to use `QMutableHashIterator::remove()`. In addition, you can clear the entire hash using `clear()`.
&emsp;&emsp;`QHash's` key and value data types must be assignable data types. You cannot, for example, store a `QWidget` as a value; instead, store a `QWidget *`. In addition, `QHash's` key type must provide `operator==()`, and there must also be a global `qHash()` function that returns a hash value for an argument of the key's type.
&emsp;&emsp;Here's a list of the `C++` and `Qt` types that can serve as keys in a `QHash`: any integer type (`char`, `unsigned long`, etc.), any pointer type, `QChar`, `QString`, and `QByteArray`. For all of these, the `QHash` header defines a `qHash()` function that computes an adequate hash value. If you want to use other types as the key, make sure that you provide `operator==()` and a `qHash()` implementation.

``` cpp
#ifndef EMPLOYEE_H
#define EMPLOYEE_H
​
class Employee {
public:
    Employee() {}
    Employee ( const QString &name, const QDate &dateOfBirth );
    ...
private:
    QString myName;
    QDate myDateOfBirth;
};
​
inline bool operator== ( const Employee &e1, const Employee &e2 ) {
    return e1.name() == e2.name() && e1.dateOfBirth() == e2.dateOfBirth();
}
​
inline uint qHash ( const Employee &key ) {
    return qHash ( key.name() ) ^ key.dateOfBirth().day();
}
​
#endif // EMPLOYEE_H
```

&emsp;&emsp;The `qHash()` function computes a numeric value based on a key. It can use any algorithm imaginable, as long as it always returns the same value if given the same argument. In other words, if `e1 == e2`, then `qHash(e1) == qHash(e2)` must hold as well. However, to obtain good performance, the `qHash()` function should attempt to return different hash values for different keys to the largest extent possible.
&emsp;&emsp;In the example above, we've relied on `Qt's` global `qHash(const QString &)` to give us a hash value for the employee's name, and `XOR'ed` this with the day they were born to help produce unique hashes for people with the same name.
&emsp;&emsp;Internally, `QHash` uses a hash table to perform lookups. Unlike `Qt 3's` `QDict` class, which needed to be initialized with a prime number, `QHash's` hash table automatically grows and shrinks to provide fast lookups without wasting too much memory. You can still control the size of the hash table by calling `reserve()` if you already know approximately how many items the `QHash` will contain, but this isn't necessary to obtain good performance. You can also call `capacity()` to retrieve the hash table's size.

### Member Type Documentation

- typedef `QHash::ConstIterator`: `Qt-style` synonym for `QHash::const_iterator`.
- typedef `QHash::Iterator`: `Qt-style` synonym for `QHash::iterator`.
- typedef `QHash::difference_type`: Typedef for `ptrdiff_t`. Provided for `STL` compatibility.
- typedef `QHash::key_type`: Typedef for `Key`. Provided for `STL` compatibility.
- typedef `QHash::mapped_type`: Typedef for `T`. Provided for `STL` compatibility.
- typedef `QHash::size_type`: Typedef for `int`. Provided for `STL` compatibility.

### Member Function Documentation

- `QHash::QHash()`: Constructs an empty hash.
- `QHash::QHash(const QHash<Key, T> & other)`: Constructs a copy of `other`. This operation occurs in constant time, because `QHash` is implicitly shared. This makes returning a `QHash` from a function very fast. If a shared instance is modified, it will be copied (`copy-on-write`), and this takes linear time.
- `QHash::~QHash()`: Destroys the hash. References to the values in the hash and all iterators of this hash become invalid.
- `iterator QHash::begin()`: Returns an `STL-style` iterator pointing to the first item in the hash.
- `const_iterator QHash::begin() const`: This is an overloaded function.
- `int QHash::capacity() const`: Returns the number of buckets in the `QHash's` internal hash table. The sole purpose of this function is to provide a means of fine tuning `QHash's` memory usage. In general, you will rarely ever need to call this function. If you want to know how many items are in the hash, call `size()`.
- `void QHash::clear()`: Removes all items from the hash.
- `const_iterator QHash::constBegin() const`: Returns a const `STL-style` iterator pointing to the first item in the hash.
- `const_iterator QHash::constEnd() const`: Returns a const `STL-style` iterator pointing to the imaginary item after the last item in the hash.
- `const_iterator QHash::constFind(const Key & key) const`: Returns an iterator pointing to the item with the `key` in the hash. If the hash contains no item with the `key`, the function returns `constEnd()`.
- `bool QHash::contains(const Key & key) const`: Returns `true` if the hash contains an item with the `key`; otherwise returns `false`.
- `int QHash::count(const Key & key) const`: Returns the number of items associated with the `key`.
- `int QHash::count() const`: This is an overloaded function. Same as `size()`.
- `bool QHash::empty() const`: This function is provided for `STL` compatibility. It is equivalent to `isEmpty()`, returning `true` if the hash is empty; otherwise returns `false`.
- `iterator QHash::end()`: Returns an `STL-style` iterator pointing to the imaginary item after the last item in the hash.
- `const_iterator QHash::end() const`: This is an overloaded function.
- `iterator QHash::erase(iterator pos)`: Removes the `(key, value)` pair associated with the iterator `pos` from the hash, and returns an iterator to the next item in the hash. Unlike `remove()` and `take()`, this function never causes `QHash` to rehash its internal data structure. This means that it can safely be called while iterating, and won't affect the order of items in the hash. For example:

``` cpp
QHash<QObject *, int> objectHash;
...
QHash<QObject *, int>::iterator i = objectHash.find ( obj );
​
while ( i != objectHash.end() && i.key() == obj ) {
    if ( i.value() == 0 ) {
        i = objectHash.erase ( i );
    } else {
        ++i;
    }
}
```

- `iterator QHash::find(const Key & key)`: Returns an iterator pointing to the item with the `key` in the hash. If the hash contains no item with the `key`, the function returns `end()`. If the hash contains multiple items with the `key`, this function returns an iterator that points to the most recently inserted value. The other values are accessible by incrementing the iterator. For example, here's some code that iterates over all the items with the same `key`:

``` cpp
QHash<QString, int> hash;
...
QHash<QString, int>::const_iterator i = hash.find ( "HDR" );
​
while ( i != hash.end() && i.key() == "HDR" ) {
    cout << i.value() << endl;
    ++i;
}
```

- `const_iterator QHash::find(const Key & key) const`: This is an overloaded function.
- `iterator QHash::insert(const Key & key, const T & value)`: Inserts a new item with the `key` and a `value` of value. If there is already an item with the `key`, that item's value is replaced with `value`. If there are multiple items with the `key`, the most recently inserted item's value is replaced with `value`.
- `iterator QHash::insertMulti(const Key & key, const T & value)`: Inserts a new item with the `key` and a `value` of value. If there is already an item with the same `key` in the hash, this function will simply create a new one (This behavior is different from `insert()`, which overwrites the `value` of an existing item).
- `bool QHash::isEmpty() const`: Returns `true` if the hash contains no items; otherwise returns `false`.
- `const Key QHash::key(const T & value) const`: Returns the first key mapped to `value`. If the hash contains no item with the `value`, the function returns a `default-constructed` key. This function can be slow (linear time), because `QHash's` internal data structure is optimized for fast lookup by key, not by value.
- `const Key QHash::key(const T & value, const Key & defaultKey) const`: This is an overloaded function. Returns the first key mapped to `value`, or `defaultKey` if the hash contains no item mapped to `value`. This function can be slow (linear time), because `QHash's` internal data structure is optimized for fast lookup by key, not by value.
- `QList<Key> QHash::keys() const`: Returns a list containing all the keys in the hash, in an arbitrary order. Keys that occur multiple times in the hash (because items were inserted with `insertMulti()`, or `unite()` was used) also occur multiple times in the list. To obtain a list of unique keys, where each key from the map only occurs once, use `uniqueKeys()`. The order is guaranteed to be the same as that used by `values()`.
- `QList<Key> QHash::keys(const T & value) const`: This is an overloaded function. Returns a list containing all the keys associated with `value`, in an arbitrary order. This function can be slow (linear time), because `QHash's` internal data structure is optimized for fast lookup by key, not by value.
- `int QHash::remove(const Key & key)`: Removes all the items that have the `key` from the hash. Returns the number of items removed which is usually `1` but will be `0` if the `key` isn't in the hash, or greater than `1` if `insertMulti()` has been used with the `key`.
- `void QHash::reserve(int size)`: Ensures that the `QHash's` internal hash table consists of at least `size` buckets. This function is useful for code that needs to build a huge hash and wants to avoid repeated reallocation. For example:

``` cpp
QHash<QString, int> hash;
hash.reserve ( 20000 );
​
for ( int i = 0; i < 20000; ++i ) {
    hash.insert ( keys[i], values[i] );
}
```

Ideally, `size` should be slightly more than the maximum number of items expected in the hash. `size` doesn't have to be prime, because `QHash` will use a prime number internally anyway. If `size` is an underestimate, the worst that will happen is that the `QHash` will be a bit slower. In general, you will rarely ever need to call this function. `QHash's` internal hash table automatically shrinks or grows to provide good performance without wasting too much memory.

- `int QHash::size() const`: Returns the number of items in the hash.
- `void QHash::squeeze()`: Reduces the size of the `QHash's` internal hash table to save memory. The sole purpose of this function is to provide a means of fine tuning `QHash's` memory usage. In general, you will rarely ever need to call this function.
- `void QHash::swap(QHash<Key, T> & other)`: Swaps hash `other` with this hash. This operation is very fast and never fails.
- `T QHash::take(const Key & key)`: Removes the item with the `key` from the hash and returns the value associated with it. If the item does not exist in the hash, the function simply returns a `default-constructed` value. If there are multiple items for key in the hash, only the most recently inserted one is removed. If you don't use the return value, `remove()` is more efficient.
- `QList<Key> QHash::uniqueKeys() const`: Returns a list containing all the keys in the map. Keys that occur multiple times in the map (because items were inserted with `insertMulti()`, or `unite()` was used) occur only once in the returned list.
- `QHash<Key, T> & QHash::unite(const QHash<Key, T> & other)`: Inserts all the items in the `other` hash into this hash. If a key is common to both hashes, the resulting hash will contain the key multiple times.
- `const T QHash::value(const Key & key) const`: Returns the value associated with the `key`. If the hash contains no item with the `key`, the function returns a `default-constructed` value. If there are multiple items for the key in the hash, the value of the most recently inserted one is returned.
- `const T QHash::value(const Key & key, const T & defaultValue) const`: This is an overloaded function. If the hash contains no item with the given `key`, the function returns `defaultValue`.
- `QList<T> QHash::values() const`: Returns a list containing all the values in the hash, in an arbitrary order. If a key is associated multiple values, all of its values will be in the list, and not just the most recently inserted one. The order is guaranteed to be the same as that used by `keys()`.
- `QList<T> QHash::values(const Key & key) const`: This is an overloaded function. Returns a list of all the values associated with the `key`, from the most recently inserted to the least recently inserted.
- `bool QHash::operator!=(const QHash<Key, T> & other) const`: Returns `true` if `other` is not equal to this hash; otherwise returns `false`. Two hashes are considered equal if they contain the same `(key, value)` pairs. This function requires the value type to implement `operator==()`.
- `QHash<Key, T> & QHash::operator=(const QHash<Key, T> & other)`: Assigns `other` to this hash and returns a reference to this hash.
- `bool QHash::operator==(const QHash<Key, T> & other) const`: Returns `true` if `other` is equal to this hash; otherwise returns `false`. Two hashes are considered equal if they contain the same `(key, value)` pairs. This function requires the value type to implement `operator==()`.
- `T & QHash::operator[](const Key & key)`: Returns the value associated with the `key` as a modifiable reference. If the hash contains no item with the `key`, the function inserts a `default-constructed` value into the hash with the `key`, and returns a reference to it. If the hash contains multiple items with the `key`, this function returns a reference to the most recently inserted value.
- `const T QHash::operator[](const Key & key) const`: This is an overloaded function. Same as `value()`.

### Related Non-Members

- `uint qHash(const QXmlNodeModelIndex & index)`: Computes a hash key from the `QXmlNodeModelIndex` `index`, and returns it. This function would be used by `QHash` if you wanted to build a hash table for instances of `QXmlNodeModelIndex`. The hash is computed on `QXmlNodeModelIndex::data()`, `QXmlNodeModelIndex::additionalData()`, and `QXmlNodeModelIndex::model()`. This means the hash key can be used for node indexes from different node models.
- `uint qHash(char key)`: Returns the hash value for the `key`.
- `uint qHash(uchar key)`: Returns the hash value for the `key`.
- `uint qHash(signed char key)`: Returns the hash value for the `key`.
- `uint qHash(ushort key)`: Returns the hash value for the `key`.
- `uint qHash(short key)`: Returns the hash value for the `key`.
- `uint qHash(uint key)`: Returns the hash value for the `key`.
- `uint qHash(int key)`: Returns the hash value for the `key`.
- `uint qHash(ulong key)`: Returns the hash value for the `key`.
- `uint qHash(long key)`: Returns the hash value for the `key`.
- `uint qHash(quint64 key)`: Returns the hash value for the `key`.
- `uint qHash(qint64 key)`: Returns the hash value for the `key`.
- `uint qHash(QChar key)`: Returns the hash value for the `key`.
- `uint qHash(const QByteArray & key)`: Returns the hash value for the `key`.
- `uint qHash(const QString & key)`: Returns the hash value for the `key`.
- `uint qHash(const QBitArray & key)`: Returns the hash value for the `key`.
- `uint qHash(const T * key)`: Returns the hash value for the `key`.
- `uint qHash(const QPair<T1, T2> & key)`: Returns the hash value for the `key`. Types `T1` and `T2` must be supported by `qHash()`.
- `QDataStream & operator<<(QDataStream & out, const QHash<Key, T> & hash)`: Writes the `hash` to stream `out`. This function requires the key and value types to implement `operator<<()`.
- `QDataStream & operator>>(QDataStream & in, QHash<Key, T> & hash)`: Reads a hash from stream `in` into `hash`. This function requires the key and value types to implement `operator>>()`.