---
title: CSharp继承
date: 2020-03-21 12:54:37
categories: C#
---
&emsp;&emsp;继承是面向对象程序设计中最重要的概念之一。继承允许我们根据一个类来定义另一个类，这使得创建和维护应用程序变得更容易。同时也有利于重用代码和节省开发时间。<!--more-->
&emsp;&emsp;当创建一个类时，程序员不需要完全重新编写新的数据成员和成员函数，只需要设计一个新的类，继承了已有的类的成员即可。这个已有的类被称为`基类`，这个新的类被称为`派生类`。

### 基类和派生类

&emsp;&emsp;`C#`创建派生类的语法如下：

``` cs
class <基类> {
    /* ... */
}

class <派生类> : <基类> {
    /* ... */
}
```

假设有一个基类`Shape`，它的派生类是`Rectangle`：

``` cs
using System;

namespace InheritanceApplication {
    class Shape {
        public void setWidth(int w) {
            width = w;
        }

        public void setHeight(int h) {
            height = h;
        }

        protected int width;
        protected int height;
    }

    class Rectangle : Shape { /* 派生类 */
        public int getArea() {
            return (width * height);
        }
    }

    class RectangleTester {
        static void Main(string[] args) {
            Rectangle Rect = new Rectangle();

            Rect.setWidth(5);
            Rect.setHeight(7);
            Console.WriteLine("总面积：{0}", Rect.getArea());
        }
    }
}
```

执行结果：

``` cs
总面积：35
```

### 访问基类

&emsp;&emsp;如果派生类需要访问基类的成员，可以使用`base`关键字：

``` cs
using System;

class SomeClass { /* 基类 */
    public string Field1 = "Fields -- In the base class";
}

class OtherClass : SomeClass { /* 继承于SomeClass */
    new public string Field1 = "Fields -- In the derived class";

    public void PrintField1() {
        Console.WriteLine(Field1); /* 访问派生类 */
        Console.WriteLine(base.Field1); /* 访问基类 */
    }
}

class Program {
    static void Main() {
        OtherClass oc = new OtherClass();
        oc.PrintField1();
    }
}
```

执行结果：

``` cs
Fields -- In the derived class
Fields -- In the base class
```

### 基类的初始化

&emsp;&emsp;派生类继承了基类的成员变量和成员方法，因此父类对象应在子类对象创建之前被创建。你可以在成员初始化列表中进行父类的初始化。

``` cs
using System;

namespace RectangleApplication {
    class Rectangle {
        protected double length;
        protected double width;

        public Rectangle(double l, double w) {
            length = l;
            width = w;
        }

        public double GetArea() {
            return length * width;
        }

        public void Display() {
            Console.WriteLine("长度：{0}", length);
            Console.WriteLine("宽度：{0}", width);
            Console.WriteLine("面积：{0}", GetArea());
        }
    }

    class Tabletop : Rectangle {
        private double cost;

        public Tabletop(double l, double w) : base(l, w) {
        }

        public double GetCost() {
            double cost;
            cost = GetArea() * 70;
            return cost;
        }

        public void Display() {
            base.Display();
            Console.WriteLine("成本：{0}", GetCost());
        }
    }

    class ExecuteRectangle {
        static void Main(string[] args) {
            Tabletop t = new Tabletop(4.5, 7.5);
            t.Display();
        }
    }
}
```

执行结果：

``` cs
长度：4.5
宽度：7.5
面积：33.75
成本：2362.5
```