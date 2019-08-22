---
title: strdup函数
date: 2018-12-10 16:31:53
categories: C语言语法详解
---
&emsp;&emsp;函数原型如下：

``` cpp
#include <string.h>
char* strdup ( char* str );
```

参数`str`为待复制的字符串，函数功能为实现复制字符串`str`。若成功，则返回复制的新字符串的指针，失败返回`NULL`。

``` cpp
#include <string.h>
#include <stdio.h>

int main() {
    char* str = "Hello World";
    char* strtemp;
    /* 复制字符串str，复制完成后返回字符串的指针保存在strtemp中 */
    strtemp = strdup ( str );
    printf ( "%s", strtemp );
    return 0;
}
```

&emsp;&emsp;这个函数在`Linux`的`man`手册里解释为：

``` bash
The strdup() function returns a pointer toa new string which is a duplicate of the string s.
Memory for thenew string is obtained with malloc(3), and can be freed with free(3).
The strndup() function is similar, but onlycopies at most n characters.
If s is longer than n, only ncharacters are copied, and a terminating NUL is added.
```

`strdup`主要是拷贝字符串`s`的一个副本，由函数返回值返回，这个副本有自己的内存空间，和`s`不相关。`strdup`函数复制一个字符串，使用完后要记得删除在函数中动态申请的内存，`strdup`函数的参数不能为`NULL`，一旦为`NULL`，就会报段错误，因为该函数包括了`strlen`函数，而该函数参数不能是`NULL`。
&emsp;&emsp;`strdup`的工作原理如下：

``` cpp
char* __strdup ( const char* s ) {
    size_t len = strlen ( s ) + 1;
    void* new = malloc ( len );

    if ( new == NULL ) {
        return NULL;
    }

    return ( char* ) memcpy ( new, s, len );
}
```

实例`1`如下：

``` cpp
#include <stdio.h>
#include <string.h>
#include <alloc.h>

int main ( void ) {
    char* dup_str, *string = "abcde";
    dup_str = strdup ( string );
    printf ( "%s\n", dup_str );
    free ( dup_str );
    return 0;
}
```

实例`2`如下：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>

unsigned int Test() {
    charbuf[] = "Hello,World!";
    char* pb = strndup ( buf, strlen ( buf ) );
    return ( unsigned int ) ( pb );
}

int main() {
    unsigned int pch = Test();
    printf ( "Testing:%s\n", ( char* ) pch );
    free ( ( void* ) pch );
    return 0;
}
```

### strdup与strcpy函数的区别

&emsp;&emsp;它们的共同点是两个函数都实现了字符串的拷贝，不同点是`strcpy`函数是把从`src`地址开始且含有`NUL`结束符的字符串复制到以`dest`开始的地址空间。

``` cpp
char* strcpy ( char* strdest, const char* strsrc ) {
    assert ( ( strdest != NULL ) && ( strsrc != NULL ) );
    char* address = strdest;

    while ( ( *strdest++ = *strstrc++ ) != ’\0’ );

    return address;
}
```

&emsp;&emsp;由`strcpy`和`strdup`函数实现可知：`strdup`函数返回指向被复制的字符串的指针，所需空间由`malloc`函数分配且可以由`free`函数释放。`stdrup`可以直接把要复制的内容复制给没有初始化的指针，因为它会自动分配空间给目的指针。`strcpy`的目的指针一定是已经分配好的内存指针。
&emsp;&emsp;`strdup`的缺点是：使用`strdup`函数的时候，往往会忘记内存的释放，因为申请内存空间的动作是在`strdup`函数内实现，如果对该函数的实现不是很了解，则会忘记使用`free`函数来释放空间。