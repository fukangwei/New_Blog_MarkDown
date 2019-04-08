---
title: strdup函数
date: 2018-12-10 16:31:53
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[strdup函数的用法](https://blog.csdn.net/Leichelle/article/details/7465769)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;函数原型如下所示：

``` c
#include <string.h>
char* strdup ( char* str );
```

参数str为待复制的字符串，函数功能为实现复制字符串str。若成功，则返回复制的新字符串的指针，失败返回NULL。

``` c
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

&emsp;&emsp;这个函数在Linux的man手册里解释为：

``` bash
The strdup() function returns a pointer toa new string which is a duplicate of the string s.
Memory for thenew string is obtained with malloc(3), and can be freed with free(3).
The strndup() function is similar, but onlycopies at most n characters.
If s is longer than n, only ncharacters are copied, and a terminating NUL is added.
```

strdup主要是拷贝字符串s的一个副本，由函数返回值返回，这个副本有自己的内存空间，和s不相关。strdup函数复制一个字符串，使用完后要记得删除在函数中动态申请的内存，strdup函数的参数不能为NULL，一旦为NULL，就会报段错误，因为该函数包括了strlen函数，而该函数参数不能是NULL。
&emsp;&emsp;strdup的工作原理如下：

``` c
char* __strdup ( const char* s ) {
    size_t len = strlen ( s ) + 1;
    void* new = malloc ( len );

    if ( new == NULL ) {
        return NULL;
    }

    return ( char* ) memcpy ( new, s, len );
}
```

实例1如下所示：

``` c
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

实例2如下所示：

``` c
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

&emsp;&emsp;它们的共同点是两个函数都实现了字符串的拷贝，不同点是strcpy函数是把从src地址开始且含有`NUL`结束符的字符串复制到以dest开始的地址空间。其实现如下所示：

``` c
char* strcpy ( char* strdest, const char* strsrc ) {
    assert ( ( strdest != NULL ) && ( strsrc != NULL ) );
    char* address = strdest;

    while ( ( *strdest++ = *strstrc++ ) != ’\0’ );

    return address;
}
```

&emsp;&emsp;由strcpy和strdup函数实现可知：strdup函数返回指向被复制的字符串的指针，所需空间由malloc函数分配且可以由free函数释放。stdrup可以直接把要复制的内容复制给没有初始化的指针，因为它会自动分配空间给目的指针。strcpy的目的指针一定是已经分配好的内存指针。
&emsp;&emsp;strdup的缺点是：使用strdup函数的时候，往往会忘记内存的释放，因为申请内存空间的动作是在strdup函数内实现，如果对该函数的实现不是很了解，则会忘记使用free函数来释放空间。