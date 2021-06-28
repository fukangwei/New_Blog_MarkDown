---
title: math库文件
date: 2021-03-03 05:36:21
categories: C语言语法详解
---
### floor

&emsp;&emsp;功能是把一个数字向下取整，即如果数字是`2.2`，那么向下取整的结果就为`2.000000`。<!--more-->

``` cpp
double floor ( doube x );
```

使用示例：

``` cpp
#include <stdio.h>
#include <math.h>

int main() {
    double i = floor ( 2.2 );
    double j = floor ( -2.2 );
    printf ( "The floor of 2.2 is %f\n", i );
    printf ( "The floor of -2.2 is %f\n", j );
    return 0;
}
```

执行结果：

``` bash
The floor of 2.2 is 2.000000
The floor of -2.2 is -3.000000
```

### ceil

&emsp;&emsp;功能是把一个数字向上取整，即如果数字是`2.2`，那么向上取整的结果就为`3.000000`。

``` cpp
double ceil ( doube x );
```

使用示例：

``` cpp
#include <stdio.h>
#include <math.h>

int main() {
    double i = ceil ( 2.2 );
    double j = ceil ( -2.2 );
    printf ( "The ceil of 2.2 is %f\n", i );
    printf ( "The ceil of -2.2 is %f\n", j );
    return 0;
}
```

执行结果：

``` bash
The ceil of 2.2 is 3.000000
The ceil of -2.2 is -2.000000
```

### round

&emsp;&emsp;功能是把一个数字四舍五入，即如果数字是`2.2`，那么四舍五入的结果就为`2`；如果数字是`2.5`，那么结果就是`3`。

``` cpp
double round ( doube x );
```

使用示例：

``` cpp
#include <stdio.h>
#include <math.h>

int main() {
    double i = round ( 2.2 );
    double x = round ( 2.7 );
    double j = round ( -2.2 );
    double y = round ( -2.7 );
    printf ( "The round of 2.2 is %f\n", i );
    printf ( "The round of 2.7 is %f\n", x );
    printf ( "The round of -2.2 is %f\n", j );
    printf ( "The round of -2.7 is %f\n", y );
    return 0;
}
```

执行结果：

``` bash
The round of 2.2 is 2.000000
The round of 2.7 is 3.000000
The round of -2.2 is -2.000000
The round of -2.7 is -3.000000
```

### pow

&emsp;&emsp;函数原型如下：

``` cpp
double pow ( double x, double y );
```

该函数用来求`x`的`y`次幂。

``` cpp
#include <stdio.h>
#include <math.h>

int main() {
    double x = 4, y = 6;
    double result = pow ( x, y );
    printf ( "%lf\n", result );
    return 0;
}
```

### abs

&emsp;&emsp;函数原型如下：

``` cpp
int abs ( int x );
```

该函数返回`x`的绝对值。

``` cpp
#include <stdio.h>
#include <stdlib.h>

int main () {
    int a, b;
    a = abs ( 5 );
    printf ( "a = %d\n", a );
    b = abs ( -10 );
    printf ( "b = %d\n", b );
    return ( 0 );
}
```

### sqrt

&emsp;&emsp;函数原型如下：

``` cpp
double sqrt ( double x );
```

该函数返回`x`的平方根。

``` cpp
#include <stdio.h>
#include <math.h>

int main() {
    double root;
    root = sqrt ( 200 );
    printf ( "answer is %f\n", root );
    return 0;
}
```