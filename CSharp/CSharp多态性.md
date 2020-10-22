---
title: CSharp多态性
date: 2020-03-21 13:19:18
categories: C#
---
&emsp;&emsp;多态是同一个行为具有多个不同表现形式或形态的能力。多态性意味着有多重形式，在面向对象编程范式中，多态性往往表现为`一个接口，多个功能`。<!--more-->
&emsp;&emsp;例如我们按下`F1`键：

1. 如果当前在`Word`下，弹出的就是`Word`帮助。
2. 如果当前在`Flash`界面下，弹出的就是`AS 3`的帮助文档。

&emsp;&emsp;多态性可以是静态的或动态的：

1. 在静态多态性中，函数的响应是在`编译`时发生的。
2. 在动态多态性中，函数的响应是在`运行`时发生的。

&emsp;&emsp;在`C#`中，每个类型都是多态的，因为包括用户定义类型在内的所有类型都继承自`Object`。

### 静态多态性

&emsp;&emsp;在编译时，函数和对象的连接机制被称为`早期绑定`，也被称为`静态绑定`。`C#`提供了`2`种技术来实现静态多态性，分别为`函数重载`和`运算符重载`。

#### 函数重载

&emsp;&emsp;你可以在同一个范围内对相同的函数名有多个定义。函数的定义必须彼此不同，可以是参数列表中的参数类型不同，也可以是参数个数不同。不能重载只有返回类型不同的函数声明。
&emsp;&emsp;下面的实例演示了几个相同的函数`Add`，用于对不同个数参数进行相加处理：

``` cs
using System;

namespace PolymorphismApplication {
    public class TestData {
        public int Add(int a, int b, int c) {
            return a + b + c;
        }

        public int Add(int a, int b) {
            return a + b;
        }
    }

    class Program {
        static void Main(string[] args) {
            TestData dataClass = new TestData();
            int add1 = dataClass.Add(1, 2);
            int add2 = dataClass.Add(1, 2, 3);

            Console.WriteLine("add1：" + add1);
            Console.WriteLine("add2：" + add2);
        }
    }
}
```

&emsp;&emsp;下面的实例演示了几个相同的函数`print`，用于打印不同的数据类型：

``` cs
using System;

namespace PolymorphismApplication {
    class Printdata {
        void print(int i) {
            Console.WriteLine("输出整型：{0}", i);
        }

        void print(double f) {
            Console.WriteLine("输出浮点型：{0}" , f);
        }

        void print(string s) {
            Console.WriteLine("输出字符串：{0}", s);
        }

        static void Main(string[] args) {
            Printdata p = new Printdata();
            p.print(1); /* 调用print来打印整数 */
            p.print(1.23); /* 调用print来打印浮点数 */
            p.print("Hello Runoob"); /* 调用print来打印字符串*/
        }
    }
}
```

执行结果：

``` cs
输出整型：1
输出浮点型：1.23
输出字符串：Hello Runoob
```

### 动态多态性

&emsp;&emsp;`C#`允许使用关键字`abstract`创建抽象类。抽象类包含抽象方法，抽象方法可以被派生类实现。
&emsp;&emsp;请注意，下面是有关抽象类的一些规则：

1. 不能创建一个抽象类的实例。
2. 不能在一个抽象类外部声明一个抽象方法。
3. 通过在类定义前面放置关键字`sealed`，可以将类声明为`密封类`。当一个类被声明为`sealed`时，它不能被继承。抽象类不能被声明为`sealed`。

``` cs
using System;

namespace PolymorphismApplication {
    abstract class Shape {
       abstract public int area();
    }

    class Rectangle: Shape {
        private int length;
        private int width;

        public Rectangle(int a = 0, int b = 0) {
            length = a;
            width = b;
        }

        public override int area() {
            Console.WriteLine("Rectangle类的面积：");
            return (width * length);
        }
    }

    class RectangleTester {
        static void Main(string[] args) {
            Rectangle r = new Rectangle(10, 7);
            double a = r.area();
            Console.WriteLine("面积：{0}",a);
        }
   }
}
```

执行结果：

``` cs
Rectangle类的面积：
面积：70
```