---
title: enum枚举
categories: C++语法详解
date: 2019-02-05 17:55:40
---
&emsp;&emsp;请看下面的语句：<!--more-->

``` cpp
enum enumType {
    Monday,
    Tuesday,
    Wednesday,
    Thursday,
    Friday,
    Saturday,
    Sunday
};
```

上述代码有两个作用：声明`enumType`为新的数据类型，称为`枚举`(`enumeration`)；声明`Monday`、`Tuesday`等为符号常量，通常称之为枚举量，其值默认分别为`0`至`6`。
&emsp;&emsp;利用新的枚举类型`enumType`声明这种类型的变量：

``` cpp
enumType Weekday;
```

然而与基本变量类型不同的是，在不进行强制转换的前提下，只能将定义的枚举量赋值给该种枚举的变量：

``` cpp
Weekday = Monday;
```

不能将其他值赋给枚举变量，但可以利用强制类型转换将其他类型值赋给枚举变量：

``` cpp
Weekday = 10; /* 这是不允许的，因为10不是枚举量 */
Weekday = enumType ( 20 ); /* 这是可以的，但不推荐 */
```

可以将枚举量赋给非枚举变量，因为枚举量是符号常量，编译器会自动把枚举量转换为`int`类型：

``` cpp
int a = Monday;
```

&emsp;&emsp;我们可以显式地设置枚举量的值，指定的值必须是整数：

``` cpp
enum enumType {
    Monday = 1,
    Tuesday = 2,
    Wednesday = 3,
    Thursday = 4,
    Friday = 5,
    Saturday = 6,
    Sunday = 7
};
```

也可以只显式地定义一部分枚举量的值：

``` cpp
enum enumType {
    Monday = 1,
    Tuesday,
    Wednesday = 1,
    Thursday,
    Friday,
    Saturday,
    Sunday
};
```

`Monday`、`Wednesday`均被定义为`1`，`Tuesday`为`2`，`Thursday`、`Friday`、`Saturday`、`Sunday`的值默认分别为`2`、`3`、`4`、`5`。未被初始化的枚举值的值默认将比其前面的枚举值大`1`。注意，枚举量的值可以是相同的。
&emsp;&emsp;枚举和`switch`是最好的搭档：

``` cpp
enum enumType {
    Step0,
    Step1,
    Step2
} Step = Step0;

switch ( Step ) {
    case Step0:
        /* ... */
        break;
    case Step1:
        /* ... */
        break;
    case Step2:
        /* ... */
        break;
    default:
        break;
}
```