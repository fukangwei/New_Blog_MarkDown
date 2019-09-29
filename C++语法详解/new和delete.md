---
title: new和delete
categories: C++语法详解
abbrlink: 56c2f768
date: 2019-02-07 10:44:54
---
&emsp;&emsp;In the `C++` programming language, `new` and `delete` are a pair of language constructs that perform dynamic memory allocation, object construction and object destruction.

### Overview

&emsp;&emsp;Except for a form called the `placement new`, the `new` operator denotes a request for memory allocation on a process's heap. If sufficient memory is available, `new` initialises the memory, calling object constructors if necessary, and returns the address to the newly allocated and initialised memory. A `new` request, in its simplest form, looks as follows:

``` cpp
p = new T;
```

where `p` is a previously declared pointer of type `T` (or some other type to which a `T` pointer can be assigned, such as a superclass of `T`). The default constructor for `T`, if any, is called to construct a `T` instance in the allocated memory buffer.
&emsp;&emsp;If not enough memory is available in the free store for an object of type `T`, the `new` request indicates failure by throwing an exception of type `std::bad_alloc`. This removes the need to explicitly check the result of an allocation.
&emsp;&emsp;The deallocation counterpart of `new` is `delete`, which first calls the destructor (if any) on its argument and then returns the memory allocated by `new` back to the free store. Every call to `new` must be matched by a call to `delete`; failure to do so causes memory leaks.
&emsp;&emsp;`new` syntax has several variants that allow finer control over memory allocation and object construction. A function `call-like` syntax is used to call a different constructor than the default one and pass it arguments, e.g.,

``` cpp
p = new T ( argument );
```

calls a `single-argument` `T` constructor instead of the default constructor when initializing the newly allocated buffer.
&emsp;&emsp;A different variant allocates and initialises arrays of objects rather than single objects:

``` cpp
p = new T [N];
```

This requests a memory buffer from the free store that is large enough to hold a contiguous array of `N` objects of type `T`, contiguously, and calls the default constructor on each elements of the array.
&emsp;&emsp;Memory allocated with the `new[]` must be deallocated with the `delete[]` operator, rather than `delete`. Using the inappropriate form results in undefined behavior. `C++` compilers are not required to generate a diagnostic message for using the wrong form.
&emsp;&emsp;The `C++11` standard specifies an additional syntax,

``` cpp
p = new T[N] {initializer_1, ..., initializer_N};
```

that initializes each `p[i]` to `initializer_i + 1`.

### Error handling

&emsp;&emsp;If `new` cannot find sufficient memory to service an allocation request, it can report its error in three distinct ways. Firstly, the `ISO C++ standard` allows programs to register a custom function called a `new_handler` with the `C++` runtime; if it does, then this function is called whenever `new` encounters an error. The `new_handler` may attempt to make more memory available, or terminate the program if it can't.
&emsp;&emsp;If no `new_handler` is installed, `new` instead throws an exception of type `std::bad_alloc`. Thus, the program does not need to check the value of the returned pointer, as is the habit in `C`; if no exception was thrown, the allocation succeeded.
&emsp;&emsp;The third method of error handling is provided by the variant form `new(std::nothrow)`, which specifies that no exception should be thrown; instead, a `null` pointer is returned to signal an allocation error.

### Overloading

&emsp;&emsp;The `new` operator can be overloaded so that specific types (classes) use custom memory allocation algorithms for their instances. For example, the following is a variant of the singleton pattern where the first new `Singleton` call allocates an instance and all subsequent calls return this same instance:

``` cpp
class Singleton {
    static Singleton *instance;
    static std::size_t refcount;
​
public:
    static void *operator new ( std::size_t nbytes ) throw ( std::bad_alloc ) {
        if ( instance == nullptr ) {
            instance = ::new Singleton; /* Use the default allocator */
        }
​
        refcount++;
        return instance;
    }
​
    static void operator delete ( void *p ) {
        if ( --refcount == 0 ) {
            ::delete instance;
            instance = nullptr;
        }
    }
};
​
Singleton *Singleton::instance = nullptr;
std::size_t Singleton::refcount = 0;
```

&emsp;&emsp;This feature was available from early on in `C++'s` history, although the specific overloading mechanism changed. It was added to the language because `object-oriented` `C++` programs tended to allocate many small objects with `new`, which internally used the `C` allocator; that, however, was optimized for the fewer and larger allocations performed by typical `C` programs. Stroustrup reported that in early applications, the `C` function malloc was `the most common performance bottleneck in real systems`, with programs spending up to `50%` of their time in this function.

### void* operator new(size_t size)

&emsp;&emsp;The `C++` language construct that only allocates memory is called `void* operator new(size_t size)`. It is used by `new` in the allocation phase. It can be overridden per class or globally to define a specific memory allocator.

### Relation to malloc and free

&emsp;&emsp;Since standard `C++` subsumes the `C` standard library, the `C` dynamic memory allocation routines `malloc`, `realloc` and `free` are also available to `C++` programmers. The use of these routines is discouraged for most uses, since they do not perform object initialization and destruction. `new` and `delete` were, in fact, introduced in the first version of `C++` (then called `C with Classes`) to avoid the necessity of manual object initialization.
&emsp;&emsp;In contrast to the `C` routines, which allow growing or shrinking an allocated array with `realloc`, it is not possible to change the size of a memory buffer allocated by `new[]`. The `C++` standard library instead provides a dynamic array that can be extended or reduced in its `std::vector` template class.

&emsp;&emsp;**补充说明**：
&emsp;&emsp;1. `delete`只会调用一次析构函数，而`delete[]`会调用每一个成员的析构函数。在`More Effective C++`中有更为详细的解释：当`delete`操作符用于数组时，它为每个数组元素调用析构函数，然后调用`delete`来释放内存。`delete`与`new`配套使用，`delete []`与`new []`配套使用。
&emsp;&emsp;2. 对于`int *pi1 = new int;`，进行的是默认初始化，`*pi1`的值未定义；对于`int *pi2 = new int(1024);`，`*p2`的值初始化为`1024`；对于`delete p;`，`p`必须指向一个动态分配的对象或是一个空指针。
&emsp;&emsp;3. 对于`typedef int arrT[42]; int *p = new arrT;`，它是分配一个`42`个`int`的动态数组，`p`指向第一个`int`。动态数组不是数组类型，不能对动态数组调用`begin`或`end`。对于`delete [] p;`，`p`必须指向一个动态分配的数组或为空。
&emsp;&emsp;4. 对于`int *p2 = new int[10]();`，不仅申请了空间，而且初始化为`0`；对于`int *p1 = new int[10];`，只申请空间，没有进行初始化。