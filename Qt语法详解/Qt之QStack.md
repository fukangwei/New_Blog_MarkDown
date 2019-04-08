---
title: Qt之QStack
date: 2019-01-02 19:05:18
categories: Qt语法详解
---
### QStack Class

&emsp;&emsp;The `QStack` class is a template class that provides a `stack`.

Header   | Inherits
---------|---------
`QStack` | `QVector<T>`

**Note**: All functions in this class are reentrant.

### Public Functions

``` cpp
          QStack()
          ~QStack()
T         pop()
void      push(const T & t)
void      swap(QStack<T> & other)
T &       top()
const T & top() const
```

### Detailed Description

&emsp;&emsp;The `QStack` class is a template class that provides a stack. `QStack<T>` is one of Qt's generic container classes. It implements a stack data structure for items of a same type.
&emsp;&emsp;A stack is a last in, first out (`LIFO`) structure. Items are added to the top of the stack using `push()` and retrieved from the top using `pop()`. The `top()` function provides access to the topmost item without removing it. Example:

``` cpp
QStack<int> stack;
stack.push ( 1 );
stack.push ( 2 );
stack.push ( 3 );
​
while ( !stack.isEmpty() ) {
    cout << stack.pop() << endl;
}
```

The example will output `3, 2, 1` in that order.
&emsp;&emsp;`QStack` inherits from `QVector`. All of QVector's functionality also applies to `QStack`. For example, you can use `isEmpty()` to test whether the stack is empty, and you can traverse a `QStack` using QVector's iterator classes (for example, `QVectorIterator`). But in addition, `QStack` provides three convenience functions that make it easy to implement LIFO semantics: `push()`, `pop()`, and `top()`.
&emsp;&emsp;QStack's value type must be an assignable data type. This covers most data types that are commonly used, but the compiler won't let you, for example, store a `QWidget` as a value; instead, store a `QWidget *`.

### Member Function Documentation

- `QStack::QStack()`: Constructs an empty stack.
- `QStack::~QStack()`: Destroys the stack. References to the values in the stack, and all iterators over this stack, become invalid.
- `T QStack::pop()`: Removes the top item from the stack and returns it. This function assumes that the stack isn't empty.
- `void QStack::push(const T & t)`: Adds element `t` to the top of the stack. This is the same as `QVector::append()`.
- `void QStack::swap(QStack<T> & other)`: Swaps stack `other` with this stack. This operation is very fast and never fails.
- `T & QStack::top()`: Returns a reference to the stack's `top` item. This function assumes that the stack isn't empty. This is the same as `QVector::last()`.
- `const T & QStack::top() const`: This is an overloaded function.