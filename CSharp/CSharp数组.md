---
title: CSharp数组
date: 2020-03-21 08:19:51
categories: C#
---
&emsp;&emsp;数组是一个存储相同类型元素的固定大小的顺序集合。数组是用来存储数据的集合，通常认为数组是一个同一类型变量的集合。<!--more-->

### 声明数组

&emsp;&emsp;在`C#`中声明一个数组，你可以使用下面的语法：

``` cs
datatype[] arrayName;
```

- `datatype`：指定被存储在数组中的元素的类型。
- `[]`：指定数组的维度。
- `arrayName`：指定数组的名称。

### 初始化数组

&emsp;&emsp;声明一个数组不会在内存中初始化数组。当初始化数组变量时，你可以赋值给数组。
&emsp;&emsp;数组是一个`引用类型`，所以你需要使用`new`关键字来创建数组的实例：

``` cs
double[] balance = new double[10];
```

### 赋值给数组

&emsp;&emsp;你可以通过使用索引号`[]`赋值给一个单独的数组元素：

``` cs
double[] balance = new double[10];
balance[0] = 4500.0;
```

你可以在声明数组的同时给数组赋值：

``` cs
double[] balance = {2340.0, 4523.69, 3421.0};
```

你也可以创建并初始化一个数组：

``` cs
int [] marks = new int[5] {99, 98, 92, 97, 95};
```

在上述情况下，你也可以省略数组的大小：

``` cs
int [] marks = new int[] {99, 98, 92, 97, 95};
```

你也可以赋值一个数组变量到另一个目标数组变量中。在这种情况下，目标和源会指向相同的内存位置：

``` cs
int [] marks = new int[] {99, 98, 92, 97, 95};
int[] score = marks;
```

当你创建一个数组时，`C#`编译器会根据数组类型隐式初始化每个数组元素为一个默认值。例如，`int`数组的所有元素都会被初始化为`0`。

### 访问数组元素

&emsp;&emsp;元素是通过带索引的数组名称来访问的：

``` cs
double salary = balance[9];
```

示例如下：

``` cs
using System;

namespace ArrayApplication {
    class MyArray {
        static void Main(string[] args) {
            int[] n = new int[10]; /* n 是一个带有10个整数的数组 */
            int i, j;

            for (i = 0; i < 10; i++) { /* 初始化数组n中的元素 */
                n[i] = i + 100;
            }

            for (j = 0; j < 10; j++) { /* 输出每个数组元素的值 */
                Console.WriteLine("Element[{0}] = {1}", j, n[j]);
            }
        }
    }
}
```

### 使用foreach循环

&emsp;&emsp;你也可以使用一个`foreach`语句来遍历数组：

``` cs
using System;

namespace ArrayApplication {
    class MyArray {
        static void Main(string[] args) {
            int[] n = new int[10]; /* n 是一个带有10个整数的数组 */

            for (int i = 0; i < 10; i++) { /* 初始化数组n中的元素 */
                n[i] = i + 100;
            }

            foreach (int j in n) { /* 输出每个数组元素的值 */
                int i = j - 100;
                Console.WriteLine("Element[{0}] = {1}", i, j);
            }
        }
    }
}
```

### 多维数组

&emsp;&emsp;`C#`支持多维数组，多维数组又称为矩形数组。你可以声明一个`string`类型的二维数组：

``` cs
string [,] names;
```

或者你可以声明一个`int`类型的三维数组：

``` cs
int [, ,] m;
```

#### 初始化二维数组

&emsp;&emsp;多维数组可以通过在括号内为每行指定值来进行初始化。下面是一个带有`3`行`4`列的数组。

``` cs
int [,] a = new int [3, 4] {
    {0, 1, 2, 3} , /* 初始化索引号为0的行 */
    {4, 5, 6, 7} , /* 初始化索引号为1的行 */
    {8, 9, 10, 11} /* 初始化索引号为2的行 */
};
```

#### 访问二维数组元素

&emsp;&emsp;二维数组中的元素是通过使用下标(即数组的行索引和列索引)来访问的：

``` cs
int val = a[2, 3];
```

上面的语句将获取数组中第`3`行第`4`个元素。

``` cs
using System;

namespace ArrayApplication {
    class MyArray {
        static void Main(string[] args) {
            int[,] a = new int[5, 2] { /* 一个带有5行2列的数组 */
                {0, 0}, {1, 2}, {2, 4},
                {3, 6}, {4, 8} };
            int i, j;

            for (i = 0; i < 5; i++) { /* 输出数组中每个元素的值 */
                for (j = 0; j < 2; j++) {
                    Console.WriteLine("a[{0},{1}] = {2}", i, j, a[i, j]);
                }
            }
        }
    }
}
```

### 交错数组

&emsp;&emsp;交错数组是数组的数组，实际上是一维数组。交错数组与二维数组的区别，可以直观地理解为交错数组每一行的长度是可以不一样的。你可以声明一个带有`int`类型的交错数组 `scores`：

``` cs
int [][] scores;
```

声明一个数组不会在内存中创建数组。创建上面的数组：

``` cs
int[][] scores = new int[5][];
for (int i = 0; i < scores.Length; i++) {
    scores[i] = new int[4];
}
```

你可以初始化一个交错数组：

``` cs
int[][] scores = new int[2][]{
    new int[]{92, 93, 94},
    new int[]{85, 66, 87, 88}
};
```

其中，`scores`是一个由两个整型数组组成的数组：

1. `scores[0]`是一个带有`3`个整数的数组。
2. `scores[1]`是一个带有`4`个整数的数组。

``` cs
using System;

namespace ArrayApplication {
    class MyArray {
        static void Main(string[] args) {
            /* 一个由5个整型数组组成的交错数组 */
            int[][] a = new int[][]{
                new int[]{0, 0}, new int[]{1, 2}, new int[]{2, 4},
                new int[]{3, 6}, new int[]{4, 8}};
            int i, j;

            for (i = 0; i < 5; i++) { /* 输出数组中每个元素的值 */
                for (j = 0; j < 2; j++) {
                    Console.WriteLine("a[{0}][{1}] = {2}", i, j, a[i][j]);
                }
            }
        }
    }
}
```

### 传递数组给函数

&emsp;&emsp;在`C#`中，你可以传递数组作为函数的参数：

``` cs
using System;

namespace ArrayApplication {
    class MyArray {
        double getAverage(int[] arr) {
            double avg;
            int sum = 0;

            foreach (int member in arr) {
                sum += member;
            }

            avg = (double)sum / arr.Length;
            return avg;
        }

        static void Main(string[] args) {
            MyArray app = new MyArray();
            /* 一个带有5个元素的int数组 */
            int[] balance = new int[] {1000, 2, 3, 17, 50};
            double avg;

            avg = app.getAverage(balance); /* 传递数组的指针作为参数 */
            Console.WriteLine("平均值是：{0}", avg);
        }
    }
}
```

### Array类

&emsp;&emsp;`Array`类是`C#`中所有数组的基类，它是在`System`命名空间中定义。`Array`类提供了各种用于数组的属性和方法。

#### Array类的属性

&emsp;&emsp;以下列出了`Array`类中一些最常用的属性：

- `IsFixedSize`：获取一个值，该值指示数组是否带有固定大小。
- `IsReadOnly`：获取一个值，该值指示数组是否只读。
- `Length`：获取一个`32`位整数，该值表示所有维度的数组中的元素总数。
- `LongLength`：获取一个`64`位整数，该值表示所有维度的数组中的元素总数。
- `Rank`：获取数组的维度。

#### Array类的方法

&emsp;&emsp;以下列出了`Array`类中一些最常用的方法：

- `Clear`：根据元素的类型，设置数组中某个范围的元素为`0`、`false`或者`null`。
- `Copy(Array, Array, Int32)`：从数组的第一个元素开始复制某个范围的元素到另一个数组的第一个元素位置。长度由一个`32`位整数指定。
- `CopyTo(Array, Int32)`：从当前的一维数组中复制所有的元素到一个指定的一维数组的指定索引位置。索引由一个`32`位整数指定。
- `GetLength`：获取一个`32`位整数，该值表示指定维度的数组中的元素总数。
- `GetLongLength`：获取一个`64`位整数，该值表示指定维度的数组中的元素总数。
- `GetLowerBound`：获取数组中指定维度的下界。
- `GetType`：获取当前实例的类型，从`Object`继承。
- `GetUpperBound`：获取数组中指定维度的上界。
- `GetValue(Int32)`：获取一维数组中指定位置的值，索引由一个`32`位整数指定。
- `IndexOf(Array, Object)`：搜索指定的对象，返回整个一维数组中第一次出现的索引。
- `Reverse(Array)`：逆转整个一维数组中元素的顺序。
- `SetValue(Object, Int32)`：给一维数组中指定位置的元素设置值，索引由一个`32`位整数指定。
- `Sort(Array)`：使用数组的每个元素的`IComparable`实现来排序整个`一维数组`中的元素。
- `ToString`：返回一个表示当前对象的字符串，从`Object`继承。

``` cs
using System;

namespace ArrayApplication {
    class MyArray {
        static void Main(string[] args) {
            int[] list = {34, 72, 13, 44, 25, 30, 10};

            Console.Write("原始数组：");
            foreach (int i in list) {
                Console.Write(i + " ");
            }

            Console.WriteLine();

            Array.Reverse(list); /* 逆转数组 */
            Console.Write("逆转数组：");
            foreach (int i in list) {
                Console.Write(i + " ");
            }

            Console.WriteLine();

            Array.Sort(list); /* 排序数组 */
            Console.Write("排序数组：");
            foreach (int i in list) {
                Console.Write(i + " ");
            }
        }
    }
}
```

执行结果：

``` cs
原始数组：34 72 13 44 25 30 10
逆转数组：10 30 25 44 13 72 34
排序数组：10 13 25 30 34 44 72
```