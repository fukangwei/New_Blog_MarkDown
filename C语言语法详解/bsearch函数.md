---
title: bsearch函数
date: 2018-12-12 00:18:48
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[C语言中用bsearch](https://blog.csdn.net/hermito/article/details/51622321)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;C语言中可以用bsearch实现二分查找，同qsort一样，bsearch也包含在库中，且同样要自定义比较子函数：

``` c
void* bsearch ( const void* key, const void* base, size_t nmem, size_t size, int ( *comp ) ( const void*, const void* ) );
```

key指向所要查找的元素；base指向进行查找的数组；nmem为查找长度，一般为数组长度；size为每个元素所占的字节数，一般用`sizeof(...)`表示；comp指向比较子函数，它定义比较的规则。需要注意的是，数据必须是经过预先排序的，而排序的规则要和comp所指向比较子函数的规则相同。如果查找成功，则返回数组中匹配元素的地址，反之则返回空。对于有多于一个的元素匹配成功的情况，bsearch未定义返回哪一个。示例如下所示：

``` c
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

``` bash
found
```