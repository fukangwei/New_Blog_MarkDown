---
title: C作用域规则
categories: C语言语法详解
date: 2019-10-02 09:29:44
---
&emsp;&emsp;作用域是程序中定义的变量所存在的区域，超过该区域变量就不能被访问。<!--more-->
&emsp;&emsp;`C`语言中有三个地方可以声明变量：

- 在函数或块内部的局部变量。
- 在所有函数外部的全局变量。
- 在形式参数的函数参数定义中。

### 局部变量

&emsp;&emsp;在某个函数或块的内部声明的变量称为`局部变量`。它们只能被该函数或该代码块内部的语句使用。
&emsp;&emsp;下面是使用局部变量的实例，其中变量`a`、`b`和`c`是`main`函数的局部变量：

``` cpp
#include <stdio.h>

int main () {
    /* 局部变量声明 */
    int a, b;
    int c;
}
```

### 全局变量

&emsp;&emsp;全局变量是定义在函数外部，通常是在程序的顶部。全局变量在整个程序生命周期内都是有效的，在任意的函数内部能访问全局变量。

``` cpp
#include <stdio.h>

int g; /* 全局变量声明 */

int main () {
    int a, b; /* 局部变量声明 */
}
```

&emsp;&emsp;局部变量和全局变量的名称可以相同。在函数内部，如果某个局部变量和全局变量名字相同，则函数会使用局部变量。

``` cpp
#include <stdio.h>

int g = 20; /* 全局变量声明 */

int main() {
    int g = 10; /* 局部变量声明 */
    printf ( "value of g = %d\n", g );
    return 0;
}
```

执行结果：

``` cpp
value of g = 10
```

### 形式参数

&emsp;&emsp;函数的形式参数会被当作该函数内的局部变量。如果与全局变量同名，则函数会使用形式参数。

``` cpp
#include <stdio.h>

int sum ( int, int );

int main() {
    int a = 10;
    int b = 20;
    int c = 0;
    printf ( "value of a in main = %d\n", a );
    c = sum ( a, b );
    printf ( "value of c in main = %d\n", c );
    return 0;
}

int sum ( int a, int b ) {
    printf ( "value of a in sum = %d\n", a );
    printf ( "value of b in sum = %d\n", b );
    return a + b;
}
```

执行结果：

``` cpp
value of a in main = 10
value of a in sum = 10
value of b in sum = 20
value of c in main = 30
```

### 变量初始化

&emsp;&emsp;定义局部变量时，系统不会对其初始化，其内容是个任意值。
&emsp;&emsp;定义全局变量时，系统会对其初始化：

数据类型   | 初始化默认值
----------|------------
`int`     | `0`
`char`    | `'\0'`
`float`   | `0`
`double`  | `0`
`pointer` | `NULL`