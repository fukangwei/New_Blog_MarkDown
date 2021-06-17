---
title: bsearch函数
categories: C语言语法详解
date: 2018-12-12 00:18:48
---
&emsp;&emsp;可以使用`bsearch`实现二分查找，函数原型如下：<!--more-->

``` cpp
void* bsearch ( const void* key, const void* base, size_t nmem, size_t size, int ( *comp ) ( const void *, const void * ) );
```

- `key`：指向需要查找的元素。
- `base`：指向进行查找的数组。
- `nmem`：要搜索的数据范围，一般为数组长度。
- `size`：每个元素所占的字节数。
- `comp`：指向比较子函数，它定义比较的规则。

需要注意的是，数据必须是经过预先排序的，而排序的规则要和`comp`所指向比较子函数的规则相同。
&emsp;&emsp;对于`bsearch`函数的返回值，有如下情况：

1. 如果查找成功，则返回数组中匹配元素的地址。
2. 如果查找成功，则返回`null`。

对于有多于一个的元素匹配成功的情况，`bsearch`未定义返回哪一个。

``` cpp
#include <stdio.h>
#include <stdlib.h>

#define NUM 8

int compare ( const void* p, const void* q ) {
    return ( * ( int* ) p - * ( int* ) q );
}

int main ( int argc, char* argv[] ) {
    int array[NUM] = {9, 2, 7, 11, 3, 87, 34, 6};
    int key = 3;
    int* p;
    qsort ( array, NUM, sizeof ( int ), compare );
    p = ( int* ) bsearch ( &key, array, NUM, sizeof ( int ), compare );
    ( p == NULL ) ? puts ( "not found" ) : puts ( "found" );
    return 0;
}
```

执行结果：

``` cpp
found
```