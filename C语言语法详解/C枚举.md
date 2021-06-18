---
title: C枚举
categories: C语言语法详解
date: 2019-10-02 10:31:14
---
&emsp;&emsp;枚举是`C`语言中的一种基本数据类型，它可以让数据更简洁：<!--more-->

``` cpp
enum 枚举名 {枚举元素1, 枚举元素2, ...};
```

第一个枚举成员的默认值为整型的`0`，后续枚举成员的值则是在前一个成员上加`1`。
&emsp;&emsp;使用枚举的方式定义一周：

``` cpp
enum DAY {
    MON = 1, TUE, WED, THU, FRI, SAT, SUN
};
```

这个实例把`MON`的值定义为`1`，把`TUE`定义为`2`，以此类推。
&emsp;&emsp;使用枚举的方式定义四季：

``` cpp
enum season {
    spring, summer = 3, autumn, winter
};
```

`spring`的值为`0`，`summer`为`3`，`autumn`为`4`，`winter`为`5`。

### 枚举变量的定义

&emsp;&emsp;可以通过以下`3`种方式来定义枚举变量：

- 先定义枚举类型，再定义枚举变量：

``` cpp
enum DAY {
    MON = 1, TUE, WED, THU, FRI, SAT, SUN
};

enum DAY day;
```

- 定义枚举类型的同时定义枚举变量：

``` cpp
enum DAY {
    MON = 1, TUE, WED, THU, FRI, SAT, SUN
} day;
```

- 省略枚举名称，直接定义枚举变量：

``` cpp
enum {
    MON = 1, TUE, WED, THU, FRI, SAT, SUN
} day;
```

&emsp;&emsp;实例如下：

``` cpp
#include <stdio.h>

enum DAY {
    MON = 1, TUE, WED, THU, FRI, SAT, SUN
};

int main() {
    enum DAY day;
    day = WED;
    printf ( "%d\n", day ); /* 输出“3” */
    return 0;
}
```

### 枚举遍历

&emsp;&emsp;在`C`语言中，枚举类型是被当做`int`或者`unsigned int`类型来处理的，所以按照`C`语言规范是没有办法遍历枚举类型的。

### 枚举与switch

&emsp;&emsp;枚举在`switch`中的使用：

``` cpp
#include <stdio.h>
#include <stdlib.h>

int main() {
    enum color { red = 1, green, blue };
    enum color favorite_color;
    printf ( "请输入你喜欢的颜色: (1. red, 2. green, 3. blue): " );
    scanf ( "%d", &favorite_color );

    switch ( favorite_color ) {
        case red:
            printf ( "你喜欢的颜色是红色" );
            break;

        case green:
            printf ( "你喜欢的颜色是绿色" );
            break;

        case blue:
            printf ( "你喜欢的颜色是蓝色" );
            break;

        default:
            printf ( "你没有选择你喜欢的颜色" );
    }

    return 0;
}
```

### 将整数转换为枚举

&emsp;&emsp;以下实例将整数转换为枚举：

``` cpp
#include <stdio.h>
#include <stdlib.h>

int main() {
    enum day {
        saturday,  sunday,   monday, tuesday,
        wednesday, thursday, friday
    } workday;
    int a = 1;
    enum day weekend;
    weekend = ( enum day ) a; /* 类型转换 */
    // weekend = a; /* 错误 */
    printf ( "weekend: %d\n", weekend );
    return 0;
}
```

执行结果：

``` cpp
weekend: 1
```