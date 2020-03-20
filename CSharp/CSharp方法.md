---
title: CSharp方法
date: 2020-03-21 07:13:06
categories: C#
---
&emsp;&emsp;一个方法是把一些相关的语句组织在一起，用来执行一个任务的语句块。每一个`C#`程序至少有一个带有`Main`方法的类。<!--more-->

### 定义方法

&emsp;&emsp;`C#`定义方法的语法如下：

``` cs
<Access Specifier> <Return Type> <Method Name> (Parameter List) {
    Method Body
}
```

- `Access Specifier`：访问修饰符，这个决定了变量或方法对于另一个类的可见性。
- `Return Type`：返回类型，一个方法可以返回一个值。返回类型是方法返回的值的数据类型。如果方法不返回任何值，则返回类型为`void`。
- `Method Name`：方法名称，是一个唯一的标识符，且是大小写敏感的。它不能与类中声明的其他标识符相同。
- `Parameter List`：参数列表，使用圆括号括起来，该参数是用来传递和接收方法的数据。参数列表是指方法的参数类型、顺序和数量。参数是可选的，也就是说，一个方法可能不包含参数。
- `Method Body`：方法主体，包含了完成任务所需的指令集。

&emsp;&emsp;下面的代码片段显示一个函数`FindMax`，它接受两个整数值，并返回两个中的较大值。它有`public`访问修饰符，所以它可以使用类的实例从类的外部进行访问。

``` cs
class NumberManipulator {
    public int FindMax(int num1, int num2) {
        int result; /* 局部变量声明 */

        if (num1 > num2)
            result = num1;
        else
            result = num2;

        return result;
    }
}
```

### 调用方法

&emsp;&emsp;你可以使用方法名调用方法：

``` cs
using System;

namespace CalculatorApplication {
    class NumberManipulator {
        public int FindMax(int num1, int num2) {
            int result;

            if (num1 > num2)
                result = num1;
            else
                result = num2;

            return result;
        }

        static void Main(string[] args) {
            int a = 100;
            int b = 200;
            int ret;
            NumberManipulator n = new NumberManipulator();

            ret = n.FindMax(a, b); /* 调用FindMax方法 */
            Console.WriteLine("最大值是：{0}", ret);
        }
    }
}
```

&emsp;&emsp;你也可以使用类的实例从另一个类中调用其他类的公有方法。例如，方法`FindMax`属于`NumberManipulator`类，你可以从另一个类`Test`中调用它：

``` cs
using System;

namespace CalculatorApplication {
    class NumberManipulator {
        public int FindMax(int num1, int num2) {
            int result;

            if (num1 > num2)
                result = num1;
            else
                result = num2;

            return result;
        }
    }

    class Test {
        static void Main(string[] args) {
            int a = 100;
            int b = 200;
            int ret;
            NumberManipulator n = new NumberManipulator();

            ret = n.FindMax(a, b); /* 调用FindMax方法 */
            Console.WriteLine("最大值是：{0}", ret);
        }
    }
}
```

### 递归方法调用

&emsp;&emsp;一个方法可以自我调用，这就是所谓的`递归`。下面的实例使用递归函数计算一个数的阶乘：

``` cs
using System;

namespace CalculatorApplication {
    class NumberManipulator {
        public int factorial(int num) {
            int result;

            if (num == 1) {
                return 1;
            } else {
                result = factorial(num - 1) * num;
                return result;
            }
        }

        static void Main(string[] args) {
            NumberManipulator n = new NumberManipulator();
            // 调用factorial方法
            Console.WriteLine("6 的阶乘是：{0}", n.factorial(6));
            Console.WriteLine("7 的阶乘是：{0}", n.factorial(7));
            Console.WriteLine("8 的阶乘是：{0}", n.factorial(8));
        }
    }
}
```

执行结果：

``` cs
6 的阶乘是：720
7 的阶乘是：5040
8 的阶乘是：40320
```

### 参数传递

&emsp;&emsp;当调用带有参数的方法时，你需要向方法传递参数。在`C#`中，有`3`种向方法传递参数的方式。

#### 按值传递参数

&emsp;&emsp;这是参数传递的默认方式。在这种方式下，当调用一个方法时，会为每个值参数创建一个新的存储位置。
&emsp;&emsp;实际参数的值会复制给形参，实参和形参使用的是两个不同内存中的值。所以，当形参的值发生改变时，不会影响实参的值，从而保证了实参数据的安全。

``` cs
using System;

namespace CalculatorApplication {
    class NumberManipulator {
        public void swap(int x, int y) {
            int temp;

            temp = x;
            x = y;
            y = temp;
        }

        static void Main(string[] args) {
            NumberManipulator n = new NumberManipulator();
            int a = 100;
            int b = 200;

            Console.WriteLine("在交换之前，a 的值： {0}", a);
            Console.WriteLine("在交换之前，b 的值： {0}", b);

            n.swap(a, b);
            Console.WriteLine("在交换之后，a 的值： {0}", a);
            Console.WriteLine("在交换之后，b 的值： {0}", b);
        }
    }
}
```

执行结果：

``` cs
在交换之前，a 的值：100
在交换之前，b 的值：200
在交换之后，a 的值：100
在交换之后，b 的值：200
```

#### 按引用传递参数

&emsp;&emsp;引用参数是一个对变量的内存位置的引用。当按引用传递参数时，与值参数不同的是，它不会为这些参数创建一个新的存储位置。引用参数表示与提供给方法的实际参数具有相同的内存位置。
&emsp;&emsp;在`C#`中，使用`ref`关键字声明引用参数：

``` cs
using System;

namespace CalculatorApplication {
    class NumberManipulator {
        public void swap(ref int x, ref int y) {
            int temp;

            temp = x;
            x = y;
            y = temp;
        }

        static void Main(string[] args) {
            NumberManipulator n = new NumberManipulator();
            int a = 100;
            int b = 200;

            Console.WriteLine("在交换之前，a 的值：{0}", a);
            Console.WriteLine("在交换之前，b 的值：{0}", b);

            n.swap(ref a, ref b);
            Console.WriteLine("在交换之后，a 的值：{0}", a);
            Console.WriteLine("在交换之后，b 的值：{0}", b);
        }
    }
}
```

执行结果：

``` cs
在交换之前，a 的值：100
在交换之前，b 的值：200
在交换之后，a 的值：200
在交换之后，b 的值：100
```

#### 按输出传递参数

&emsp;&emsp;`return`语句可用于只从函数中返回一个值。但是，可以使用输出参数来从函数中返回两个值。输出参数会把方法输出的数据赋给自己，其他方面与引用参数相似。

``` cs
using System;

namespace CalculatorApplication {
    class NumberManipulator {
        public void getValue(out int x) {
            int temp = 5;
            x = temp;
        }

        static void Main(string[] args) {
            NumberManipulator n = new NumberManipulator();
            int a = 100;

            Console.WriteLine("在方法调用之前，a 的值：{0}", a);
            n.getValue(out a); /* 调用函数来获取值 */
            Console.WriteLine("在方法调用之后，a 的值：{0}", a);
        }
    }
}
```

执行结果：

``` cs
在方法调用之前，a 的值：100
在方法调用之后，a 的值：5
```

&emsp;&emsp;提供给输出参数的变量不需要赋值。当需要从一个参数没有指定初始值的方法中返回值时，输出参数特别有用：

``` cs
using System;

namespace CalculatorApplication {
    class NumberManipulator {
        public void getValues(out int x, out int y) {
            Console.WriteLine("请输入第一个值：");
            x = Convert.ToInt32(Console.ReadLine());
            Console.WriteLine("请输入第二个值：");
            y = Convert.ToInt32(Console.ReadLine());
        }

        static void Main(string[] args) {
            NumberManipulator n = new NumberManipulator();
            int a, b;

            n.getValues(out a, out b); /* 调用函数来获取值 */
            Console.WriteLine("在方法调用之后，a 的值：{0}", a);
            Console.WriteLine("在方法调用之后，b 的值：{0}", b);
        }
    }
}
```

执行结果：

``` cs
请输入第一个值：
7
请输入第二个值：
8
在方法调用之后，a 的值： 7
在方法调用之后，b 的值： 8
```