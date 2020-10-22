---
title: CSharp循环
date: 2020-03-21 03:02:44
categories: C#
---
### while循环

&emsp;&emsp;语法如下：<!--more-->

``` cs
while (condition) {
    statement;
}
```

`statement`可以是一个单独的语句，也可以是几个语句组成的代码块。`condition`可以是任意的表达式，当为任意非零值时都为真。当条件为真时执行循环。

### for循环

&emsp;&emsp;语法如下：

``` cs
for (init; condition; increment) {
    statement;
}
```

&emsp;&emsp;下面是`for`循环的控制流：

1. `init`会首先被执行，且只会执行一次。这一步允许你声明并初始化任何循环控制变量。你也可以不在这里写任何语句，只要有一个分号出现即可。
2. 接下来，会判断`condition`。如果为真，则执行循环主体；如果为假，则不执行循环主体，且控制流会跳转到紧接着`for`循环的下一条语句。
3. 在执行完`for`循环主体后，控制流会跳回上面的`increment`语句。该语句允许你更新循环控制变量。该语句可以留空，只要在条件后有一个分号出现即可。
4. 条件再次被判断。如果为真，则执行循环，这个过程会不断重复(循环主体，然后增加步值，再然后重新判断条件)。在条件变为假时，`for`循环终止。

### foreach

&emsp;&emsp;`C#`也支持`foreach`循环，使用`foreach`可以迭代数组或者一个集合对象。
&emsp;&emsp;通过`foreach`循环输出整型数组中的元素：

``` cs
class ForEachTest {
    static void Main(string[] args) {
        int[] fibarray = new int[] {0, 1, 1, 2, 3, 5, 8, 13};

        foreach (int element in fibarray) {
            System.Console.WriteLine(element);
        }
    }
}
```

### do-while循环

&emsp;&emsp;`for`和`while`循环是在循环头部测试循环条件，而`do-while`循环是在循环的尾部检查它的条件。`do-while`循环会确保至少执行一次循环。

``` cs
do {
    statement;
} while (condition);
```

### 嵌套循环

&emsp;&emsp;`C#`允许在一个循环内使用另一个循环。
&emsp;&emsp;嵌套`for`循环语句的语法：

``` cs
for (init; condition; increment) {
    for (init; condition; increment) {
        statement;
    }

    statement;
}
```

&emsp;&emsp;嵌套`while`循环语句的语法：

``` cs
while (condition) {
    while (condition) {
        statement;
    }

    statement;
}
```

&emsp;&emsp;嵌套`do-while`循环语句的语法：

``` cs
do {
    statement;

    do {
        statement;
    } while (condition);
} while (condition);
```

&emsp;&emsp;关于嵌套循环，有一点值得注意，你可以在任何类型的循环内嵌套其他任何类型的循环。比如，一个`for`循环可以嵌套在一个`while`循环内，反之亦然。

### 循环控制语句

#### break

&emsp;&emsp;在`C#`中，`break`语句有以下两种用法：

1. 当`break`语句出现在一个循环内时，循环会立即终止，且程序流将继续执行紧接着循环的下一条语句。
2. 它可用于终止`switch`语句中的一个`case`。

&emsp;&emsp;如果你使用的是嵌套循环(即一个循环内嵌套另一个循环)，`break`语句会停止执行当前层的循环，然后开始执行该块之后的下一行代码。

``` cs
using System;

namespace Loops {
    class Program {
        static void Main(string[] args) {
            int a = 10;

            while (a < 20) {
                Console.WriteLine("a 的值：{0}", a);
                a++;

                if (a > 15) {
                    break; /* 使用break语句终止loop */
                }
            }
        }
    }
}
```

#### continue

&emsp;&emsp;`C#`中的`continue`语句有点像`break`语句。但它不是强迫终止，而是会跳过当前循环中的代码，强迫开始下一次循环。

1. 对于`for`循环，`continue`语句会导致执行`循环增量`和`条件测试`部分。
2. 对于`while`和`do...while`循环，`continue`语句会导致程序控制回到`条件测试`上。

``` cs
using System;

namespace Loops {
    class Program {
        static void Main(string[] args) {
            int a = 10;

            do { /* do循环执行 */
                if (a == 15) {
                    a = a + 1;
                    continue; /* 跳过迭代 */
                }

                Console.WriteLine("a 的值：{0}", a);
                a++;
            } while (a < 20);
        }
    }
}
```

### 无限循环

&emsp;&emsp;如果条件永远不为假，则循环将变成无限循环。`for`循环在传统意义上可用于实现无限循环：

``` cs
using System;

namespace Loops {
    class Program {
        static void Main(string[] args) {
            for (;;) {
                Console.WriteLine("Hey! I am Trapped");
            }
        }
    }
}
```