---
title: goto语句
date: 2018-12-06 12:42:02
categories: C语言语法详解
---
&emsp;&emsp;该博客主要来源于[C语言goto语句以及用goto语句构成循环](http://c.biancheng.net/cpp/html/42.html)，内容经过测试和修改，感谢原作者。
&emsp;&emsp;goto语句是一种无条件转移语句，其使用格式为：

``` c
goto 语句标号;
```

其中，标号是一个有效的标识符，这个标识符加上一个`:`一起出现在函数内某处。执行goto语句后，程序将跳转到该标号处并执行其后的语句。
&emsp;&emsp;注意，标号必须与goto语句同处于一个函数中，goto不能跳到函数体外的函数，即goto有局部作用域，需要在同一个栈内。通常goto语句与if条件语句连用，当满足某一条件时，程序跳到标号处运行。goto语句可以往后跳，也可以往前跳。

``` c
#include <stdio.h>

int main ( void ) {
    int i, sum = 0;
    i = 1;
loop:

    if ( i <= 100 ) {
        sum = sum + i;
        i++;
        goto loop;
    }

    printf ( "%d\n", sum );
    return 0;
}
```

&emsp;&emsp;goto语句不经常使用，主要因为它将使程序层次不清且不易读，但在多层嵌套退出时，用goto语句则比较合理。在错误处理方面，goto语句要好于结构化的语句，如下所示：

``` c
/* 结构化的语句 */
if (do_something() != ERR) {
    if (do_something2() != ERR) {
        if (do_something3() != ERR) {
            if (do_something4() != ERR) {
                ...

/* 换成goto语句 */
if (do_something() == ERR)  // 一行
    goto error;             // |
if (do_something2() == ERR) // |
    goto error;             // |
if (do_something3() == ERR) // |
    goto error;             // V
if (do_something4() == ERR) // 使用普通的平铺形式
    goto error;
```