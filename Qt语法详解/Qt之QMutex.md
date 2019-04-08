---
title: Qt之QMutex
date: 2019-01-03 20:22:01
categories: Qt语法详解
---
&emsp;&emsp;The `QMutex` class provides access serialization between threads. The header file is `QMutex`. **Note**: All functions in this class are `thread-safe`.

### Public Types

- `enum`: RecursionMode { `Recursive`, `NonRecursive` }

### Public Functions

``` cpp
     QMutex(RecursionMode mode = NonRecursive)
     ~QMutex()
void lock()
bool tryLock()
bool tryLock(int timeout)
void unlock()
```

### Detailed Description

&emsp;&emsp;The `QMutex` class provides access serialization between threads.
&emsp;&emsp;The purpose of a `QMutex` is to protect an object, data structure or section of code so that only one thread can access it at a time (this is similar to the Java synchronized keyword). It is usually best to use a mutex with a `QMutexLocker` since this makes it easy to ensure that locking and unlocking are performed consistently.
&emsp;&emsp;For example, say there is a method that prints a message to the user on two lines:

``` cpp
int number = 6;
​
void method1() {
    number *= 5;
    number /= 4;
}
​
void method2() {
    number *= 3;
    number /= 2;
}
```

If these two methods are called in succession, the following happens:

``` cpp
// method1
number *= 5; // number is now 30
number /= 4; // number is now 7
​
// method2
number *= 3; // number is now 21
number /= 2; // number is now 10
```

If these two methods are called simultaneously from two threads then the following sequence could result:

``` cpp
// Thread 1 calls method1
number *= 5; // number is now 30
​
// Thread 2 calls method2
// Most likely Thread 1 has been put to sleep
// by the operating system to allow Thread 2 to run
number *= 3; // number is now 90
number /= 2; // number is now 45
​
// Thread 1 finishes executing
number /= 4; // number is now 11, instead of 10
```

&emsp;&emsp;If we add a mutex, we should get the result we want:

``` cpp
QMutex mutex;
int number = 6;
​
void method1() {
    mutex.lock();
    number *= 5;
    number /= 4;
    mutex.unlock();
}
​
void method2() {
    mutex.lock();
    number *= 3;
    number /= 2;
    mutex.unlock();
}
```

Then only one thread can modify number at any given time and the result is correct. This is a trivial example, of course, but applies to any other case where things need to happen in a particular sequence.
&emsp;&emsp;When you call `lock()` in a thread, other threads that try to call `lock()` in the same place will block until the thread that got the lock calls `unlock()`. A `non-blocking` alternative to `lock()` is `tryLock()`.

### Member Type Documentation

- `enum`: `QMutex::RecursionMode`:

Constant | Value | Description
---------|-------|------------
`QMutex::Recursive` | `1` | In this mode, a thread can lock the same mutex multiple times and the mutex won't be unlocked until a corresponding number of `unlock()` calls have been made.
`QMutex::NonRecursive` | `0` | In this mode, a thread may only lock a mutex once.

### Member Function Documentation

- `QMutex::QMutex(RecursionMode mode = NonRecursive)`: Constructs a new mutex. The mutex is created in an unlocked state. If mode is `QMutex::Recursive`, a thread can lock the same mutex multiple times and the mutex won't be unlocked until a corresponding number of `unlock()` calls have been made. The default is `QMutex::NonRecursive`.
- `QMutex::~QMutex()`: Destroys the mutex. **Warning**: Destroying a locked mutex may result in undefined behavior.
- `void QMutex::lock()`: `Locks` the mutex. If another thread has `locked` the mutex then this call will block until that thread has unlocked it. Calling this function multiple times on the same mutex from the same thread is allowed if this mutex is a `recursive` mutex. If this mutex is a `non-recursive` mutex, this function will `dead-lock` when the mutex is locked recursively.
- `bool QMutex::tryLock()`: `Attempts` to `lock` the mutex. If the `lock` was obtained, this function returns `true`. If another thread has locked the mutex, this function returns `false` immediately. If the `lock` was obtained, the mutex must be unlocked with `unlock()` before another thread can successfully `lock` it. Calling this function multiple times on the same mutex from the same thread is allowed if this mutex is a `recursive` mutex. If this mutex is a `non-recursive` mutex, this function will always return `false` when attempting to `lock` the mutex `recursively`.
- `bool QMutex::tryLock(int timeout)`: This is an overloaded function. `Attempts` to `lock` the mutex. This function returns `true` if the lock was obtained; otherwise it returns `false`. If another thread has locked the mutex, this function will wait for at most `timeout` milliseconds for the mutex to become available. **Note**: Passing a negative number as the `timeout` is equivalent to calling `lock()`, i.e. this function will wait forever until mutex can be locked if `timeout` is negative. If the `lock` was obtained, the mutex must be unlocked with `unlock()` before another thread can successfully `lock` it. Calling this function multiple times on the same mutex from the same thread is allowed if this mutex is a `recursive` mutex. If this mutex is a `non-recursive` mutex, this function will always return `false` when attempting to lock the mutex `recursively`.
- `void QMutex::unlock()`: `Unlocks` the mutex. `Attempting` to `unlock` a mutex in a different thread to the one that locked it results in an error. Unlocking a mutex that is not locked results in undefined behavior.