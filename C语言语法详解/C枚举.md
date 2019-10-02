---
title: C枚举
categories: C语言语法详解
abbrlink: 33105
date: 2019-10-02 10:31:14
---
&emsp;&emsp;枚举是`C`语言中的一种基本数据类型，它可以让数据更简洁，更易读。

``` cpp
enum 枚举名 {枚举元素1, 枚举元素2, ...};
```

使用枚举的方式定义一个星期：

``` cpp
enum DAY {
    MON=1, TUE, WED, THU, FRI, SAT, SUN
};
```

第一个枚举成员的默认值为整型的`0`，后续枚举成员的值在前一个成员上加`1`。我们在这个实例中把第一个枚举成员的值定义为`1`，第二个就为`2`，以此类推。
&emsp;&emsp;可以在定义枚举类型时改变枚举元素的值：

``` cpp
enum season {spring, summer=3, autumn, winter};
```

没有指定值的枚举元素，其值为前一元素加`1`。也就说`spring`的值为`0`，`summer`的值为`3`，`autumn`的值为`4`，`winter`的值为`5`。

### 枚举变量的定义

&emsp;&emsp;可以通过以下三种方式来定义枚举变量

- 先定义枚举类型，再定义枚举变量：

``` cpp
enum DAY {
    MON=1, TUE, WED, THU, FRI, SAT, SUN
};

enum DAY day;
```

- 定义枚举类型的同时定义枚举变量：

``` cpp
enum DAY {
    MON=1, TUE, WED, THU, FRI, SAT, SUN
} day;
```

- 省略枚举名称，直接定义枚举变量：

``` cpp
enum {
    MON=1, TUE, WED, THU, FRI, SAT, SUN
} day;
```

实例如下：

``` cpp
#include <stdio.h>

enum DAY {
    MON = 1, TUE, WED, THU, FRI, SAT, SUN
};

int main() {
    enum DAY day;
    day = WED;
    printf ( "%d", day ); /* 输出“3” */
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
        case red:   printf ( "你喜欢的颜色是红色" ); break;
        case green: printf ( "你喜欢的颜色是绿色" ); break;
        case blue:  printf ( "你喜欢的颜色是蓝色" ); break;
        default:    printf ( "你没有选择你喜欢的颜色" );
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
    printf ( "weekend: %d", weekend ); /* 输出“weekend: 1” */
    return 0;
}
```