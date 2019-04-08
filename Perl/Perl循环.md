---
title: Perl循环
date: 2018-12-20 09:37:04
tags:
---

### while循环

&emsp;&emsp;while语句在给定条件为true时，重复执行语句或语句组，循环主体执行之前会先测试条件。语法格式如下所示：

``` perl
while ( condition ) {
    statement ( s );
}
```

其中`statement(s)`可以是一个单独的语句，也可以是几个语句组成的代码块。condition可以是任意的表达式，当条件为true时执行循环；当条件为false时，程序流将退出循环。

``` perl
$a = 1;
​
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

&emsp;&emsp;until语句在给定条件为false时，重复执行语句或语句组。语法格式如下所示：

``` perl
until ( condition ) {
    statement ( s );
}
```

### for循环

&emsp;&emsp;for循环用于多次执行一个语句序列，简化管理循环变量的代码。语法格式如下所示：

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

&emsp;&emsp;foreach循环用于迭代一个列表或集合变量的值。语法格式如下所示：

``` perl
foreach var ( list ) {
    ...
}
```

示例如下：

``` perl
@list = (2, 12, 36);
​
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

&emsp;&emsp;在Perl语言中，`do...while`循环是在循环的尾部检查它的条件。`do...while`循环与while循环类似，但是`do...while`循环会确保至少执行一次循环。语法格式如下所示：

``` perl
do{
    statement ( s );
} while ( condition );
```

示例如下：

``` perl
$a = 1;
​
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

&emsp;&emsp;Perl语言允许在一个循环内使用另一个循环，下面演示几个实例来说明这个概念。嵌套for循环语句的语法：

``` perl
for (init; condition; increment) {
    for (init; condition; increment) {
        statement(s);
    }

    statement(s);
}
```

嵌套while循环语句的语法：

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

嵌套until循环语句的语法：

``` perl
until(condition) {
    until(condition) {
        statement(s);
    }
    statement(s);
}
```

嵌套foreach循环语句的语法：

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
​
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
​
a = 1, b = 0
a = 1, b = 1
a = 2
```

### 循环控制语句

#### next语句

&emsp;&emsp;next语句用于停止执行从next语句的下一语句开始到循环体结束标识符之间的语句，转去执行continue语句块，然后再返回到循环体的起始处开始执行下一次循环。语法格式如下所示：

``` perl
next [LABEL];
```

其中LABEL是可选的，如果没有指定LABEL，next语句将返回到循环体的起始处，开始执行下一次循环。

``` perl
$a = 1;
​
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

&emsp;&emsp;last语句用于退出循环语句块，从而结束循环，last语句之后的语句不再执行，continue语句块也不再执行。语法格式如下所示：

``` perl
last
```

示例如下：

``` perl
$a = 1;
​
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

&emsp;&emsp;continue块通常在条件语句再次判断前执行，可用在while和foreach循环中。在while循环中，continue语句语法格式如下所示：

``` perl
while (condition) {
    statement(s);
}
continue {
    statement(s);
}
```

在foreach循环中，continue语句语法格式如下所示：

``` perl
foreach $a(@listA) {
    statement(s);
}
continue {
    statement(s);
}
```

while循环中使用continue语句：

``` perl
$a = 0;
​
while ($a < 3) {
    print "a = $a\n";
}
continue {
    $a = $a + 1;
}
```

执行结果：

``` bash
a = 0
a = 1
a = 2
```

foreach循环中使用continue语句：

``` perl
@list = (1, 2, 3, 4, 5);
​
foreach $a(@list) {
    print "a = $a\n";
}
continue {
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

&emsp;&emsp;redo语句直接转到循环体的第一行开始重复执行本次循环，redo语句之后的语句不再执行，continue语句块也不再执行。continue语句可用在while和foreach循环中。语法格式如下所示：

``` perl
redo [LABEL];
```

带标号修饰符LABEL的redo语句表示把循环控制流程直接转到与标号修饰符LABEL相关联的语句块的第一行处开始执行，而不再执行redo语句之后的语句和continue语句块；不带标号修饰符LABEL的redo语句表示把循环控制流程直接转到当前语句块的第一行处开始执行，而不再执行redo语句之后的语句和continue语句块；如果是在for循环中或者是带有continue语句块，则for循环中的递增列表和continue语句块都不再被执行。

``` perl
$a = 0;
​
while ($a < 6) {
    if ($a == 3) {
        $a = $a + 1;
        redo;
    }
    print "a = $a\n";
}
continue {
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

&emsp;&emsp;Perl有三种goto形式：`got LABLE`、`goto EXPR`和`goto &NAME`：

- `goto LABEL`：找出标记为LABEL的语句，并且从那里重新执行。
- `goto EXPR`：`goto EXPR`只是`goto LABEL`的一般形式。它期待表达式生成一个标记名称，并跳到该标记处执行。
- `goto &NAME`：它把正在运行着的子进程替换为一个已命名子进程的调用。

使用`goto LABEL`形式：

``` perl
$a = 1;
​
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
​
LOOP: do {
    if ($a == 3) {
        $a = $a + 1;
        goto $str1.$str2; # 类似于“goto LOOP”
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

&emsp;&emsp;如果条件永远不为false，则循环将变成无限循环。for循环在传统意义上可用于实现无限循环：

``` perl
for (;;) {
    printf "循环会无限执行\n";
}
```