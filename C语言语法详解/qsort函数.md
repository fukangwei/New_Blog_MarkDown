---
title: qsort函数
categories: C语言语法详解
date: 2018-12-12 18:38:58
---
### 函数原型

&emsp;&emsp;`qsort`是一个快速排序函数：<!--more-->

``` cpp
#include <stdlib.h>
void qsort ( void* base, int num, int width, int ( *fcmp ) ( const void*, const void* ) );
```

- `base`是待排序数组首地址。
- `num`是数组中待排序元素数量。
- `width`是各元素的占用空间大小。
- `fcmp`是指向函数的指针，用于确定排序的顺序，例如：

``` cpp
int comp ( const void* a, const void* b ) {
    return * ( int* ) a - * ( int* ) b; /* 由小到大排序 */
    // return * ( int * ) b - * ( int * ) a; /* 由大到小排序 */
}
```

### 一维数组排序

&emsp;&emsp;对`char`型一维数组进行排序：

``` cpp
#include <stdio.h>
#include <stdlib.h>

int cmp ( const void* a, const void* b ) {
    /* a、b是指向word中某个元素的指针 */
    return * ( char* ) a - * ( char* ) b;
}

int main ( void ) {
    char word[5] = {'c', 'a', 'b', 'd', 'e'};
    qsort ( word, 5, sizeof ( word[0] ), cmp );

    for ( int i = 0; i < 5; i++ ) {
        printf ( "word[%d] is %c\n", i, word[i] );
    }

    return 0;
}
```

执行结果：

``` cpp
word[0] is a
word[1] is b
word[2] is c
word[3] is d
word[4] is e
```

&emsp;&emsp;对`double`型一维数组进行排序：

``` cpp
#include <stdio.h>
#include <stdlib.h>

int cmp ( const void* a, const void* b ) {
    /* 返回值的问题，显然cmp返回的是一个整型 */
    return * ( double* ) a > * ( double* ) b ? 1 : -1;
}

int main ( void ) {
    double in[4] = {4.6, 5.2, 1.3, 0.2};
    qsort ( in, 4, sizeof ( in[0] ), cmp );

    for ( int i = 0; i < 4; i++ ) {
        printf ( "in[%d] is %lf\n", i, in[i] );
    }

    return 0;
}
```

执行结果：

``` cpp
in[0] is 0.200000
in[1] is 1.300000
in[2] is 4.600000
in[3] is 5.200000
```

### 二维数组排序

&emsp;&emsp;代码如下：

``` cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int cmp ( const void *a, const void * b ) {
    int* c = ( int * ) a;
    int* d = ( int * ) b;
    return c[0] - d[0];
}

int main() {
    int a[][2] = {{7, 3}, {4, 6}, {5, 9}};
    qsort ( a, 3, sizeof ( int ) * 2, cmp );

    for ( int i = 0; i < 3; i++ ) {
        for ( int j = 0; j < 2; j++ ) {
            printf ( "a[%d][%d] is %d ", i, j, a[i][j] );
        }

        printf ( "\n" );
    }

    return 0;
}
```

执行结果：

``` cpp
a[0][0] is 4 a[0][1] is 6
a[1][0] is 5 a[1][1] is 9
a[2][0] is 7 a[2][1] is 3
```

### 结构体一级排序

&emsp;&emsp;按结构体中某个关键字排序：

``` cpp
#include <stdio.h>
#include <stdlib.h>

struct In {
    double data;
    int other;
} s[4];

int cmp ( const void* a, const void* b ) { /* 按照data的值从小到大将结构体排序 */
    return ( * ( In* ) a ).data > ( * ( In* ) b ).data ? 1 : -1;
}

int main ( void ) {
    s[0] = {1.3, 44};
    s[1] = {4.6, 34};
    s[2] = {0.2, 22};
    s[3] = {5.2, 45};
    qsort ( s, 4, sizeof ( s[0] ), cmp );

    for ( int i = 0; i < 4; i++ ) {
        printf ( "s[%d].data is %f\n", i, s[i].data );
    }

    return 0;
}
```

执行结果：

``` cpp
s[0].data is 0.200000
s[1].data is 1.300000
s[2].data is 4.600000
s[3].data is 5.200000
```

### 结构体多级排序

&emsp;&emsp;按结构体中多个关键字排序：

``` cpp
#include <stdio.h>
#include <stdlib.h>

struct In {
    int x;
    int y;
} s[4];

/* 按照x从小到大排序，当x相等时，按照y从大到小排序 */
int cmp ( const void* a, const void* b ) {
    struct In* c = ( In* ) a;
    struct In* d = ( In* ) b;

    if ( c->x != d->x ) {
        return c->x - d->x;
    } else {
        return d->y - c->y;
    }
}

int main ( void ) {
    s[0] = {4, 44};
    s[1] = {2, 45};
    s[2] = {2, 56};
    s[3] = {1, 45};
    qsort ( s, 4, sizeof ( s[0] ), cmp );

    for ( int i = 0; i < 4; i++ ) {
        printf ( "s[%d].x is %d, s[%d].y is %d\n", i, s[i].x, i, s[i].y );
    }

    return 0;
}
```

执行结果：

``` cpp
s[0].x is 1, s[0].y is 45
s[1].x is 2, s[1].y is 56
s[2].x is 2, s[2].y is 45
s[3].x is 4, s[3].y is 44
```

### 字符串数组排序

&emsp;&emsp;对字符串数组的排序：

- 对于`char s[][]`型：

``` cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int cmp ( const void* a, const void* b ) {
    return ( strcmp ( ( char* ) a, ( char* ) b ) );
}

int main() {
    char s[][10] = {"hello", "world", "test", "litte"};
    qsort ( s, 4, sizeof ( s[0] ), cmp );

    for ( int i = 0; i < 4; i++ ) {
        printf ( "%s\n", s[i] );
    }

    return 0;
}
```

执行结果：

``` cpp
hello
litte
test
world
```

- 对于`char *s[]`型：

``` cpp
#include <stdio.h>
#include <string.h>
#include <stdlib.h>

int cmp ( const void* a, const void* b ) {
    char * c = * ( char** ) a;
    char * d = * ( char** ) b;
    return ( strcmp ( c, d ) );
}

int main() {
    char str_0[] = "hello";
    char str_1[] = "world";
    char str_2[] = "test";
    char str_3[] = "litte";
    char* s[] = {str_0, str_1, str_2, str_3};
    qsort ( s, 4, sizeof ( s[0] ), cmp );

    for ( int i = 0; i < 4; i++ ) {
        printf ( "%s\n", s[i] );
    }

    return 0;
}
```