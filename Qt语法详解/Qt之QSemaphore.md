---
title: Qt之QSemaphore
categories: Qt语法详解
abbrlink: 804b64f
date: 2019-01-03 15:47:32
---
&emsp;&emsp;The `QSemaphore` class provides a general counting semaphore. The header file is `QSemaphore`. **Note**: All functions in this class are `thread-safe`.<!--more-->

### Public Functions

Return | Function
-------|---------
       | `QSemaphore(int n = 0)`
       | `~QSemaphore()`
`void` | `acquire(int n = 1)`
`int`  | `available() const`
`void` | `release(int n = 1)`
`bool` | `tryAcquire(int n = 1)`
`bool` | `tryAcquire(int n, int timeout)`

### Detailed Description

&emsp;&emsp;The `QSemaphore` class provides a general counting semaphore. A semaphore is a generalization of a mutex. While a mutex can only be locked once, it's possible to acquire a semaphore multiple times. Semaphores are typically used to protect a certain number of identical resources. Semaphores support two fundamental operations, `acquire()` and `release()`:

- `acquire(n)` tries to acquire `n` resources. If there aren't that many resources available, the call will block until this is the case.
- `release(n)` releases `n` resources.

&emsp;&emsp;There's also a `tryAcquire()` function that returns immediately if it cannot acquire the resources, and an `available()` function that returns the number of available resources at any time. Example:

``` cpp
QSemaphore sem ( 5 ); /* sem.available() = 5 */
​
sem.acquire ( 3 ); /* sem.available() = 2 */
sem.acquire ( 2 ); /* sem.available() = 0 */
sem.release ( 5 ); /* sem.available() = 5 */
sem.release ( 5 ); /* sem.available() = 10 */
​
sem.tryAcquire ( 1 ); /* sem.available() = 9, returns true */
sem.tryAcquire ( 250 ); /* sem.available() = 9, returns false */
```

&emsp;&emsp;A typical application of semaphores is for controlling access to a circular buffer shared by a producer thread and a consumer thread. The Semaphores example shows how to use `QSemaphore` to solve that problem.
&emsp;&emsp;A `non-computing` example of a semaphore would be dining at a restaurant. A semaphore is initialized with the number of chairs in the restaurant. As people arrive, they want a seat. As seats are filled, `available()` is decremented. As people leave, the `available()` is incremented, allowing more people to enter. If a party of `10` people want to be seated, but there are only `9` seats, those `10` people will wait, but a party of `4` people would be seated (taking the available seats to `5`, making the party of `10` people wait longer).

### Member Function Documentation

- `QSemaphore::QSemaphore(int n = 0)`: Creates a new semaphore and initializes the number of resources it guards to `n` (by default, `0`).
- `QSemaphore::~QSemaphore()`: Destroys the semaphore. Warning: Destroying a semaphore that is in use may result in undefined behavior.
- `void QSemaphore::acquire(int n = 1)`: Tries to acquire `n` resources guarded by the semaphore. If `n` > `available()`, this call will block until enough resources are available.
- `int QSemaphore::available() const`: Returns the number of resources currently available to the semaphore. This number can never be negative.
- `void QSemaphore::release(int n = 1)`: Releases `n` resources guarded by the semaphore. This function can be used to `"create"` resources as well. For example:

``` cpp
QSemaphore sem ( 5 ); /* a semaphore that guards 5 resources */
sem.acquire ( 5 ); /* acquire all 5 resources */
sem.release ( 5 ); /* release the 5 resources */
sem.release ( 10 ); /* "create" 10 new resources */
```

- `bool QSemaphore::tryAcquire(int n = 1)`: Tries to acquire `n` resources guarded by the semaphore and returns `true` on success. If `available()` < `n`, this call immediately returns `false` without acquiring any resources. Example:

``` cpp
QSemaphore sem ( 5 ); /* sem.available() = 5 */
sem.tryAcquire ( 250 ); /* sem.available() = 5, returns false */
sem.tryAcquire ( 3 ); /* sem.available() = 2, returns true */
```

- `bool QSemaphore::tryAcquire(int n, int timeout)`: Tries to acquire `n` resources guarded by the semaphore and returns `true` on success. If `available()` < `n`, this call will wait for at most `timeout` milliseconds for resources to become available. **Note**: Passing a negative number as the `timeout` is equivalent to calling `acquire()`, i.e. this function will wait forever for resources to become available if `timeout` is negative. Example:

``` cpp
QSemaphore sem ( 5 ); /* sem.available() = 5 */
/* sem.available() = 5, waits 1000 milliseconds and returns false */
sem.tryAcquire ( 250, 1000 );
/* sem.available() = 2, returns true without waiting */
sem.tryAcquire ( 3, 30000 );
```