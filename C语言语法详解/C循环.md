---
title: C循环
date: 2019-10-02 08:20:06
categories: C语言语法详解
---
### while循环

&emsp;&emsp;只要给定的条件为真，`C`语言中的`while`循环语句将会重复执行：

``` cpp
while (condition) {
    statement;
}
```

`statement`可以是一个单独的语句，也可以是几个语句组成的代码块。`condition`可以是任意的表达式，当条件为`true`时执行循环；当条件为`false`时，退出循环，程序流将继续执行紧接着循环的下一条语句。

### for循环

&emsp;&emsp;`for`循环允许你编写一个执行指定次数的循环控制结构。

``` cpp
for ( init; condition; increment ) {
    statements;
}
```

下面是`for`循环的控制流：

1. `init`会首先被执行，且只会执行一次。这一步允许你声明并初始化任何循环控制变量。也可以不在这里写任何语句，只要有一个分号出现即可。
2. 接下来会判断`condition`。如果为真，则执行循环主体。如果为假，则不执行循环主体，且控制流会跳转到紧接着`for`循环的下一条语句。
3. 在执行完`for`循环主体后，控制流会跳回上面的`increment`语句。该语句允许你更新循环控制变量。该语句可以留空，只要在条件后有一个分号出现即可。
4. 条件再次被判断。如果为真，则执行循环，这个过程会不断重复。在条件变为假时，`for`循环终止。

### do-while循环

&emsp;&emsp;不像`for`和`while`循环，它们是在循环头部测试循环条件。在`C`语言中，`do-while`是在循环的尾部检查它的条件。
&emsp;&emsp;`do-while`循环与`while`循环类似，但是`do-while`循环会确保至少执行一次循环。

``` cpp
do {
    statements;
} while ( condition );
```