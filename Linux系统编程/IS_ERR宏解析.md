---
title: IS_ERR宏解析
categories: Linux系统编程
abbrlink: 4f6dcec6
date: 2019-02-02 21:48:44
---
&emsp;&emsp;最近在使用`filp_open`打开文件时遇到到一个问题，当打开一个并不存在的文件时，`filp_open`返回值为`0xfffffffe`，而并不是`0`(`NULL`)，这是因为内核对返回指针的函数做了特殊处理。内核中的函数常常返回指针，通常如果调用出错，会返回`NULL`，但`linux`做了更精妙的处理，能够通过返回的指针体现出来。<!--more-->
&emsp;&emsp;对任何一个指针，必然有三种情况：有效指针、`NULL`或者`错误指针`。而`错误指针`就是指其已经到达了最后一个`page`，比如对于`32bit`的系统来说，内核空间最高地址`0xffffffff`，那么最后一个`page`就是指的`0xfffff000`至`0xffffffff`(以`4K`大小页为例)。这段地址是被保留的，如果超过这个地址，则肯定是错误的。
&emsp;&emsp;在`linux/err.h`中包含了这一机制的处理，主要通过`IS_ERR`、`PTR_ERR`、`ERR_PTR`几个宏。

``` cpp
/*
 * Kernel pointers have redundant information, so we can use a
 * scheme where we can return either an error code or a dentry
 * pointer with the same return value.
 *
 * This should be a per-architecture thing, to allow different
 * error and pointer decisions.
 */
#define MAX_ERRNO 4095
​
#define IS_ERR_VALUE(x) unlikely((x) >= (unsigned long)-MAX_ERRNO)
​
/* 将错误号转化为指针，由于错误号在“-1000”至0间，返回的指针会落在最后一页 */
static inline void *ERR_PTR ( long error ) {
    return ( void * ) error;
}
​
/* 将指针转化为错误号 */
static inline long PTR_ERR ( const void *ptr ) {
    return ( long ) ptr;
}
​
/* 判断返回的指针是错误信息还是实际地址，即指针是否落在最后一页 */
static inline long IS_ERR ( const void *ptr ) {
    return IS_ERR_VALUE ( ( unsigned long ) ptr );
}
```

所以对于内核中返回的指针，检查错误的方式不是`if(!retptr)`，而是`if(IS_ERR(retptr))`或`if(IS_ERR_VALUE(retptr))`。

---

&emsp;&emsp;像`struct class *cls = class_create();`这种语句，其中返回的指针值并不像`kmalloc`一样这么简单，只判断是否为`NULL`就可以了，内核是返回其错误值。那么我怎么来判断它呢，总不能用`if`来将每个错误例出来吧，这里宏`IS_ERR`就发挥作用了。

``` cpp
/* include/linux/err.h */
#define IS_ERR_VALUE(x) unlikely((x) >= (unsigned long)-MAX_ERRNO)
​
static inline long __must_check IS_ERR ( const void *ptr ) {
    return IS_ERR_VALUE ( ( unsigned long ) ptr );
}
```

&emsp;&emsp;内核中的函数常常返回指针，问题是如果出错，也希望能够通过返回的指针体现出来。所幸的是，内核返回的指针一般是指向页面的边界(`4K`边界)，即`ptr & 0xfff == 0`，这样`ptr`的值不可能落在`(0xfffff000, 0xffffffff)`之间，而一般内核的出错代码也是一个小负数，在`-1000`到`0`之间，转变成`unsigned long`，正好在`(0xfffff000, 0xffffffff)`之间。因此可以用`(unsigned long)ptr > (unsigned long) - 1000L`，也就等效于`(x) >= (unsigned long) - MAX_ERRNO`，其中`MAX_ERRNO`为`4095`，来判断内核函数的返回值是一个有效的指针，还是一个出错代码。错误码的值在内核中定义都是这样的(`include/linux/errno.h`)：

``` cpp
...
#define ENOLCK  77 /* No record locks available  */
#define ENOSYS  78 /* Function not implemented   */
#define ENOMSG  80 /* No message of desired type */
#define EIDRM   81 /* Identifier removed         */
#define ENOSR   82 /* Out of streams resources   */
#define ETIME   83 /* Timer expired              */
#define EBADMSG 84 /* Not a data message         */
#define EPROTO  85 /* Protocol error             */
#define ENODATA 86 /* No data available          */
#define ENOSTR  87 /* Device not a stream        */
...
```

现在应该知道为什么写返回错误码的时候也加个负号如`-ENOSYS`这样子了。至于`PTR_ERR`、`ERR_PTR`，只是强制转换以下而已，源代码如下(`include/linux/err.h`)：

``` cpp
static inline void *__must_check ERR_PTR ( long error ) {
    return ( void * ) error;
}
​
static inline long __must_check PTR_ERR ( const void *ptr ) {
    return ( long ) ptr;
}
```

所以像上面的`cls`例子可以这样写：

``` cpp
struct class *cls = class_create ( ... );
​
if ( IS_ERR ( cls ) ) {
    ret = PTR_ERR ( cls );
    return ret;
}
```