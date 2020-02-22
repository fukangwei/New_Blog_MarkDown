---
title: Qt之QReadWriteLock
categories: Qt语法详解
date: 2019-01-03 16:28:02
---
&emsp;&emsp;The `QReadWriteLock` class provides `read-write` locking. The header file is `QReadWriteLock`. **Note**: All functions in this class are `thread-safe`.<!--more-->

### Public Types

- enum: `RecursionMode` { `Recursive`, `NonRecursive` }

### Public Functions

Return | Function
-------|---------
       | `QReadWriteLock()`
       | `QReadWriteLock(RecursionMode recursionMode)`
       | `~QReadWriteLock()`
`void` | `lockForRead()`
`void` | `lockForWrite()`
`bool` | `tryLockForRead()`
`bool` | `tryLockForRead(int timeout)`
`bool` | `tryLockForWrite()`
`bool` | `tryLockForWrite(int timeout)`
`void` | `unlock()`

### Detailed Description

&emsp;&emsp;The `QReadWriteLock` class provides `read-write` locking.
&emsp;&emsp;A `read-write` lock is a synchronization tool for protecting resources that can be accessed for `reading` and `writing`. This type of lock is useful if you want to allow multiple threads to have simultaneous `read-only` access, but as soon as one thread wants to write to the resource, all other threads must be blocked until the `writing` is complete.
&emsp;&emsp;In many cases, `QReadWriteLock` is a direct competitor to `QMutex`. `QReadWriteLock` is a good choice if there are many concurrent reads and `writing` occurs infrequently.

``` cpp
QReadWriteLock lock;

void ReaderThread::run() {
    ...
    lock.lockForRead();
    read_file();
    lock.unlock();
    ...
}

void WriterThread::run() {
    ...
    lock.lockForWrite();
    write_file();
    lock.unlock();
    ...
}
```

To ensure that writers aren't blocked forever by readers, readers attempting to obtain a lock will not succeed if there is a blocked writer waiting for access, even if the lock is currently only accessed by other readers. Also, if the lock is accessed by a writer and another writer comes in, that writer will have priority over any readers that might also be waiting.
&emsp;&emsp;Like `QMutex`, a `QReadWriteLock` can be recursively locked by the same thread when constructed in `QReadWriteLock::RecursionMode`. In such cases, `unlock()` must be called the same number of times `lockForWrite()` or `lockForRead()` was called. Note that the lock type cannot be changed when trying to lock recursively, i.e. it is not possible to lock for `reading` in a thread that already has locked for `writing` (and vice versa).

### Member Type Documentation

- enum: `QReadWriteLock::RecursionMode`

Constant                       | Value | Description
-------------------------------|-------|------------
`QReadWriteLock::Recursive`    | `1`   | In this mode, a thread can lock the same `QReadWriteLock` multiple times and the mutex won't be unlocked until a corresponding number of `unlock()` calls have been made.
`QReadWriteLock::NonRecursive` | `0`   | In this mode, a thread may only lock a `QReadWriteLock` once.

### Member Function Documentation

- `QReadWriteLock::QReadWriteLock()`: Constructs a `QReadWriteLock` object in `NonRecursive` mode.
- `QReadWriteLock::QReadWriteLock(RecursionMode recursionMode)`: Constructs a `QReadWriteLock` object in the given `recursionMode`.
- `QReadWriteLock::~QReadWriteLock()`: Destroys the `QReadWriteLock` object. **Warning**: Destroying a read-write `lock` that is in use may result in undefined behavior.
- `void QReadWriteLock::lockForRead()`: Locks the lock for reading. This function will block the current thread if any thread (including the current) has locked for writing.
- `void QReadWriteLock::lockForWrite()`: Locks the lock for writing. This function will block the current thread if another thread has locked for reading or writing.
- `bool QReadWriteLock::tryLockForRead()`: Attempts to lock for reading. If the lock was obtained, this function returns `true`, otherwise it returns `false` instead of waiting for the lock to become available, i.e. it does not block. The lock attempt will fail if another thread has locked for writing. If the lock was obtained, the lock must be unlocked with `unlock()` before another thread can successfully lock it.
- `bool QReadWriteLock::tryLockForRead(int timeout)`: This is an overloaded function. Attempts to lock for reading. This function returns `true` if the lock was obtained; otherwise it returns `false`. If another thread has locked for writing, this function will wait for at most `timeout` milliseconds for the lock to become available. **Note**: Passing a negative number as the `timeout` is equivalent to calling `lockForRead()`, i.e. this function will wait forever until lock can be locked for reading when `timeout` is negative. If the lock was obtained, the lock must be unlocked with `unlock()` before another thread can successfully lock it.
- `bool QReadWriteLock::tryLockForWrite()`: Attempts to lock for writing. If the lock was obtained, this function returns `true`; otherwise, it returns `false` immediately. The lock attempt will fail if another thread has locked for reading or writing. If the lock was obtained, the lock must be unlocked with `unlock()` before another thread can successfully lock it.
- `bool QReadWriteLock::tryLockForWrite(int timeout)`: This is an overloaded function. Attempts to lock for writing. This function returns `true` if the lock was obtained; otherwise it returns `false`. If another thread has locked for reading or writing, this function will wait for at most `timeout` milliseconds for the lock to become available. **Note**: Passing a negative number as the `timeout` is equivalent to calling `lockForWrite()`, i.e. this function will wait forever until lock can be locked for writing when `timeout` is negative. If the lock was obtained, the lock must be unlocked with `unlock()` before another thread can successfully lock it.
- `void QReadWriteLock::unlock()`: Unlocks the lock. Attempting to unlock a lock that is not locked is an error, and will result in program termination.