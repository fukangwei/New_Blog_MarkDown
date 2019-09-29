---
title: qsort函数
categories: C语言语法详解
abbrlink: fcb23fd1
date: 2018-12-12 18:38:58
---
&emsp;&emsp;`qsort`函数是编译器函数库自带的快速排序函数：

``` c
#include <stdlib.h>
void qsort ( void* base, int num, int width, int ( *fcmp ) ( const void*, const void* ) );
```

`base`是待排序数组首地址，`num`是数组中待排序元素数量，`width`是各元素的占用空间大小，`fcmp`是指向函数的指针，用于确定排序的顺序。

``` bash
MSDN: The qsort function implements a quick-sort algorithm to sort an array of num elements, each of width bytes.
The argument base is a pointer to the base of the array to be sorted. qsort overwrites this array with the sorted elements.
The argument compare is a pointer to a user-supplied routine that compares two array elements and returns a value specifying their
relationship. qsort calls the compare routine one or more times during the sort, passing pointers to two array elements on each call.
```

&emsp;&emsp;`fcmp`函数的原型为`fcmp ( ( void * ) &elem1, ( void * ) &elem2 );`，具体描述如下：

`fcmp`函数的返回值 | 描述
------------------|------
`< 0`             | `elem1`将被排在`elem2`前面
`= 0`             | `elem1`等于`elem2`
`> 0`             | `elem1`将被排在`elem2`后面

&emsp;&emsp;例如对于一个长为`1000`的数组(例如`int a[1000];`)进行排序，那么`base`为`a`，`num`为`1000`，`width`为`sizeof(int)`，`fcmp`是自己定义的函数，即`qsort ( a, 1000, sizeof ( int ), comp );`。其中，`comp`函数为：

``` cpp
int comp ( const void* a, const void* b ) {
    return * ( int* ) a - * ( int* ) b; /* 由小到大排序 */
    // return * ( int * ) b - * ( int * ) a; /* 为由大到小排序 */
}
```

参数列表是两个空指针，现在它要去指向你的数组元素，所以要转型为你当前的类型，然后取值。升序排列时，若第一个参数指针指向的`值`大于第二个参数指针指向的`值`，则返回正；若第一个参数指针指向的`值`等于第二个参数指针指向的`值`，则返回零；若第一个参数指针指向的`值`小于第二个参数指针指向的`值`，则返回负。降序排列时，则刚好相反。
&emsp;&emsp;对一维数组的排序示例(从小到大排序)如下：

``` cpp
#include <stdio.h>
#include <stdlib.h>

int comp ( const void* a, const void* b ) {
    return * ( int* ) a - * ( int* ) b;
}​

int main ( void ) {
    int i = 0;
    int* array = NULL;
    int n;
    printf ( "Input n: " );
    scanf ( "%d", &n );
    array = ( int* ) malloc ( n * sizeof ( int ) );
    printf ( "Input the elements: " );

    for ( ; i < n; i++ ) {
        scanf ( "%d", ( array + i ) );
    }

    qsort ( array, n, sizeof ( int ), comp );

    for ( i = 0; i < n; i++ ) {
        printf ( "%d ", array[i] );
    }

    free ( array );
    return 0;
}
```

&emsp;&emsp;按结构体中某个关键字排序(对结构体一级排序)：

``` cpp
struct In {
    double data;
    int other;
} s[100]​;

int cmp ( const void* a, const void* b ) { /* 按照data的值从小到大将结构体排序 */
    return ( * ( In* ) a ).data > ( * ( In* ) b ).data ? 1 : -1;
    /* 注意，这条语句在VC6.0环境下运行可能会出错，但是并不是语句错了，而是你要先Build，
       或者全部重建。或者你可以将这上面1条语句改成下面这3条语句：
    struct In *aa = (In *)a;
    struct In *bb = (In *)b;
    return aa->data > bb->data ? 1 : -1; */
}

qsort ( s, 100, sizeof ( s[0] ), cmp );
```

&emsp;&emsp;按结构体中多个关键字排序(对结构体多级排序)，以二级为例：

``` cpp
struct In {
    int x; /* 你可以理解为失败次数 */
    int y; /* 你可以比喻成成功次数 */
} s[100];

/* 按照x从小到大排序，当x相等时按照y从大到小排序。你可以理解为：失败是主要
   因素的一个问题，先比较谁的失败次数少；如果失败次数相同，再看谁的成功次数多 */
int cmp ( const void* a, const void* b ) {
    struct In* c = ( In* ) a;
    struct In* d = ( In* ) b;

    if ( c->x != d->x ) {
        return c->x - d->x;
    } else {
        return d->y - c->y;
    }
}

qsort ( s, 100, sizeof ( s[0] ), cmp );
```

&emsp;&emsp;对结构体中的字符串进行排序：

``` cpp
struct In {
    int data;
    char str[100];
} s[100];​

/* 按照结构体中字符串str的字典顺序排序 */
int cmp ( const void* a, const void* b ) {
    return strcmp ( ( * ( In* ) a )->str, ( * ( In* ) b )->str );
}

qsort ( s, 100, sizeof ( s[0] ), cmp );
```

&emsp;&emsp;`qsort`对其他类型的数组进行排序：

``` cpp
/* 对char类型数组排序 */
char word[100];

int cmp ( const void* a, const void* b ) {
    return * ( char* ) a - * ( char* ) b;
}

qsort ( word, 100, sizeof ( word[0] ), cmp );
​
/* 对double类型数组排序 */
double in[100];

int cmp ( const void* a, const void* b ) {
    /* 返回值的问题，显然cmp返回的是一个整型 */
    return * ( double* ) a > * ( double* ) b ? 1 : -1;
}

qsort ( in, 100, sizeof ( in[0] ), cmp );
```

&emsp;&emsp;对字符串数组的排序：

- 对于`char s[][]`型：

``` cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

char s[100][100];
int i, n;

int cmp ( const void* a, const void* b ) {
    return ( strcmp ( ( char* ) a, ( char* ) b ) );
}

int main() {
    scanf ( "%d", &n );

    for ( i = 0; i < n; i++ ) {
        scanf ( "%s", s[i] );
    }

    qsort ( s, n, sizeof ( s[0] ), cmp );

    for ( i = 0; i < n; i++ ) {
        printf ( "%s\n", s[i] );
    }

    return ( 0 );
}
```

- 对于`char *s[]`型：

``` cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

char* s[100];
int i, n;

int cmp ( const void* a, const void* b ) {
    return ( strcmp ( * ( char** ) a, * ( char** ) b ) );
}

int main() {
    scanf ( "%d", &n );

    for ( i = 0; i < n; i++ ) {
        s[i] = ( char* ) malloc ( 20 );
        scanf ( "%s", s[i] );
    }

    qsort ( s, n, sizeof ( s[0] ), cmp );

    for ( i = 0; i < n; i++ ) {
        printf ( "%s\n", s[i] );
    }

    for ( i = 0; i < n; i++ ) {
        free ( s[i] );
    }

    return ( 0 );
}
```

&emsp;&emsp;对一个二维数组进行排序：例如`int a[1000][2];`，按照`a[0]`的大小进行一个整体的排序，其中`a[1]`必须和`a[0]`一起移动交换，即第一行和第二行(`a[0]`和`a[1]`分别代表第一行和第二行的首地址)。使用库函数排序的代码量并不比用冒泡排序法少，但速度却快很多：

``` cpp
qsort ( a, 1000, sizeof ( int ) * 2, comp );

int comp ( const void* a, const void* b ) {
    return ( ( int* ) a ) [0] - ( ( int* ) b ) [0];
}
```