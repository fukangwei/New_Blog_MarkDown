---
title: Perl循环
categories: Perl
date: 2018-12-20 09:37:04
---
### while循环

&emsp;&emsp;`while`语句在给定条件为`true`时，重复执行语句或语句组，循环主体执行之前会先测试条件：<!--more-->

``` perl
while ( condition ) {
    statement ( s );
}
```

其中`statement(s)`可以是一个单独的语句，也可以是几个语句组成的代码块。`condition`可以是任意的表达式，当条件为`true`时执行循环；当条件为`false`时，程序流将退出循环。

``` perl
$a = 1;

while( $a < 4 ){
    printf "a的值为：$a\n";
    $a = $a + 1;
}
```

执行结果：

``` bash
a的值为：1
a的值为：2
a的值为：3
```

### until循环

&emsp;&emsp;`until`语句在给定条件为`false`时，重复执行语句或语句组：

``` perl
until ( condition ) {
    statement ( s );
}
```

### for循环

&emsp;&emsp;`for`循环用于多次执行一个语句序列：

``` perl
for ( init; condition; increment ) {
    statement ( s );
}
```

示例如下：

``` perl
for( $a = 0; $a < 4; $a = $a + 1 ){
    print "a的值为：$a\n";
}
```

执行结果：

``` bash
a的值为：0
a的值为：1
a的值为：2
a的值为：3
```

### foreach循环

&emsp;&emsp;`foreach`循环用于迭代一个列表或集合变量的值：

``` perl
foreach var ( list ) {
    ...
}
```

示例如下：

``` perl
@list = (2, 12, 36);

foreach $a ( @list ) {
    print "a的值为：$a\n";
}
```

执行结果：

``` bash
a的值为：2
a的值为：12
a的值为：36
```

### do...while循环

&emsp;&emsp;在`Perl`语言中，`do...while`循环是在循环的尾部检查它的条件。`do...while`循环与`while`循环类似，但是`do...while`循环会确保至少执行一次循环：

``` perl
do{
    statement ( s );
} while ( condition );
```

示例如下：

``` perl
$a = 1;

do{
    printf "a的值为：$a\n";
    $a = $a + 1;
} while ( $a < 4 );
```

执行结果：

``` bash
a的值为：1
a的值为：2
a的值为：3
```

### 循环嵌套

&emsp;&emsp;`Perl`语言允许在一个循环内使用另一个循环。嵌套`for`循环语句的语法：

``` perl
for (init; condition; increment) {
    for (init; condition; increment) {
        statement(s);
    }

    statement(s);
}
```

嵌套`while`循环语句的语法：

``` perl
while (condition) {
    while (condition) {
        statement(s);
    }

    statement(s);
}
```

嵌套`do...while`循环语句的语法：

``` perl
do {
    statement(s);

    do {
        statement(s);
    } while (condition);
} while (condition);
```

嵌套`until`循环语句的语法：

``` perl
until(condition) {
    until(condition) {
        statement(s);
    }

    statement(s);
}
```

嵌套`foreach`循环语句的语法：

``` perl
foreach $a(@listA) {
    foreach $b(@listB) {
        statement(s);
    }

    statement(s);
}
```

示例如下：

``` perl
$a = 0;
$b = 0;

while ($a < 2) {
    $b = 0;

    while ($b < 2) {
        print "a = $a, b = $b\n";
        $b = $b + 1;
    }

    $a = $a + 1;
    print "a = $a\n\n";
}
```

执行结果：

``` bash
a = 0, b = 0
a = 0, b = 1
a = 1

a = 1, b = 0
a = 1, b = 1
a = 2
```

### 循环控制语句

#### next语句

&emsp;&emsp;`next`语句用于停止执行从`next`语句的下一语句开始到循环体结束标识符之间的语句，转去执行`continue`语句块，然后再返回到循环体的起始处开始执行下一次循环：

``` perl
next [LABEL];
```

其中`LABEL`是可选的，如果没有指定`LABEL`，`next`语句将返回到循环体的起始处，开始执行下一次循环。

``` perl
$a = 1;

while ($a < 5) {
    if ($a == 3) {
        $a = $a + 1;
        next;
    }

    print "a的值为：$a\n";
    $a = $a + 1;
}
```

执行结果：

``` bash
a的值为：1
a的值为：2
a的值为：4
```

#### last语句

&emsp;&emsp;`last`语句用于退出循环语句块，从而结束循环，`last`语句之后的语句不再执行，`continue`语句块也不再执行：

``` perl
last
```

示例如下：

``` perl
$a = 1;

while ($a < 5) {
    if ($a == 3) {
        $a = $a + 1;
        last;
    }

    print "a的值为：$a\n";
    $a = $a + 1;
}
```

执行结果：

``` bash
a的值为：1
a的值为：2
```

#### continue语句

&emsp;&emsp;`continue`块通常在条件语句再次判断前执行，可用在`while`和`foreach`循环中。在`while`循环中，`continue`语句语法格式如下：

``` perl
while (condition) {
    statement(s);
} continue {
    statement(s);
}
```

在`foreach`循环中，`continue`语句语法格式如下：

``` perl
foreach $a(@listA) {
    statement(s);
} continue {
    statement(s);
}
```

`while`循环中使用`continue`语句：

``` perl
$a = 0;

while ($a < 3) {
    print "a = $a\n";
} continue {
    $a = $a + 1;
}
```

执行结果：

``` bash
a = 0
a = 1
a = 2
```

`foreach`循环中使用`continue`语句：

``` perl
@list = (1, 2, 3, 4, 5);

foreach $a(@list) {
    print "a = $a\n";
} continue {
    last if $a == 4;
}
```

执行结果：

``` bash
a = 1
a = 2
a = 3
a = 4
```

#### redo语句

&emsp;&emsp;`redo`语句直接转到循环体的第一行开始重复执行本次循环，`redo`语句之后的语句不再执行，`continue`语句块也不再执行。`continue`语句可用在`while`和`foreach`循环中：

``` perl
redo [LABEL];
```

带标号修饰符`LABEL`的`redo`语句表示把循环控制流程直接转到与标号修饰符`LABEL`相关联的语句块的第一行处开始执行，而不再执行`redo`语句之后的语句和`continue`语句块；不带标号修饰符`LABEL`的`redo`语句表示把循环控制流程直接转到当前语句块的第一行处开始执行，而不再执行`redo`语句之后的语句和`continue`语句块；如果是在`for`循环中或者是带有`continue`语句块，则`for`循环中的递增列表和`continue`语句块都不再被执行。

``` perl
$a = 0;

while ($a < 6) {
    if ($a == 3) {
        $a = $a + 1;
        redo;
    }

    print "a = $a\n";
} continue {
    $a = $a + 1;
}
```

执行结果：

``` bash
a = 0
a = 1
a = 2
a = 4
a = 5
```

#### goto语句

&emsp;&emsp;`Perl`有`3`种`goto`形式：`got LABLE`、`goto EXPR`和`goto &NAME`：

- `goto LABEL`：找出标记为`LABEL`的语句，并且从那里重新执行。
- `goto EXPR`：`goto EXPR`只是`goto LABEL`的一般形式。它期待表达式生成一个标记名称，并跳到该标记处执行。
- `goto &NAME`：它把正在运行着的子进程替换为一个已命名子进程的调用。

使用`goto LABEL`形式：

``` perl
$a = 1;

LOOP: do {
    if ($a == 3) {
        $a = $a + 1;
        print "跳出输出\n";
        goto LOOP;
        print "这一句不会被执行\n";
    }

    print "a = $a\n";
    $a = $a + 1;
} while ($a < 6);
```

执行结果：

``` bash
a = 1
a = 2
跳出输出
a = 4
a = 5
```

&emsp;&emsp;以下实例使用了`goto EXPR`形式，我们使用了两个字符串，并使用点号`.`来链接：

``` perl
$a = 1;
$str1 = "LO";
$str2 = "OP";

LOOP: do {
    if ($a == 3) {
        $a = $a + 1;
        goto $str1.$str2;  # 类似于“goto LOOP”
    }

    print "a = $a\n";
    $a = $a + 1;
} while ($a < 5);
```

执行结果：

``` bash
a = 1
a = 2
a = 4
```

#### 无限循环

&emsp;&emsp;如果条件永远不为`false`，则循环将变成无限循环：

``` perl
for (;;) {
    printf "循环会无限执行\n";
}
```