---
title: CSharp封装
date: 2020-03-21 06:48:45
categories: C#
---
&emsp;&emsp;封装被定义为`把一个或多个项目封闭在一个物理的或者逻辑的包中`。在面向对象程序设计方法中，封装是为了防止对实现细节的访问。<!--more-->
&emsp;&emsp;抽象和封装是面向对象程序设计的相关特性。抽象允许相关信息可视化，封装则使开发者实现所需级别的抽象。
&emsp;&emsp;`C#`封装根据具体的需要，设置使用者的访问权限，并通过访问修饰符来实现。以下是`C#`支持的访问修饰符。

### Public

&emsp;&emsp;`Public`访问修饰符允许一个类将其成员变量和成员函数暴露给其他的函数和对象。任何公有成员可以被外部的类访问。

``` cs
using System;

namespace RectangleApplication {
    class Rectangle {
        public double length;
        public double width;

        public double GetArea() {
            return length * width;
        }

        public void Display() {
            Console.WriteLine("长度：{0}", length);
            Console.WriteLine("宽度：{0}", width);
            Console.WriteLine("面积：{0}", GetArea());
        }
    }

    class ExecuteRectangle {
        static void Main(string[] args) {
            Rectangle r = new Rectangle();
            r.length = 4.5;
            r.width = 3.5;
            r.Display();
        }
    }
}
```

执行结果：

``` cs
长度：4.5
宽度：3.5
面积：15.75
```

&emsp;&emsp;在上面的实例中，成员变量`length`和`width`被声明为`public`，所以它们可以被函数`Main`使用`Rectangle`类的实例`r`访问。成员函数`Display`和`GetArea`可以直接访问这些变量。
&emsp;&emsp;成员函数`Display`也被声明为`public`，所以它也能被`Main`使用`Rectangle`类的实例`r`访问。

### Private

&emsp;&emsp;`Private`访问修饰符允许一个类将其成员变量和成员函数对其他的函数和对象进行隐藏。只有同一个类中的函数可以访问它的私有成员。即使是类的实例也不能访问它的私有成员。
&emsp;&emsp;注意，如果没有指定访问修饰符，则使用类成员的默认访问修饰符，即为`private`。

``` cs
using System;

namespace RectangleApplication {
    class Rectangle {
        private double length;
        private double width;

        public void Acceptdetails() {
            Console.WriteLine("请输入长度：");
            length = Convert.ToDouble(Console.ReadLine());
            Console.WriteLine("请输入宽度：");
            width = Convert.ToDouble(Console.ReadLine());
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

    class ExecuteRectangle {
        static void Main(string[] args) {
            Rectangle r = new Rectangle();
            r.Acceptdetails();
            r.Display();
        }
    }
}
```

执行结果：

``` cs
请输入长度：
4.4
请输入宽度：
3.3
长度：4.4
宽度：3.3
面积：14.52
```

&emsp;&emsp;在上面的实例中，成员变量`length`和`width`被声明为`private`，所以它们不能被函数`Main`访问。成员函数`AcceptDetails`和`Display`可以访问这些变量。
&emsp;&emsp;由于成员函数`AcceptDetails`和`Display`被声明为`public`，所以它们可以被`Main`使用`Rectangle`类的实例`r`访问。

### Protected

&emsp;&emsp;`Protected`访问修饰符允许`子类`访问它的`基类`的成员变量和成员函数，这样有助于实现继承。

### Internal

&emsp;&emsp;`Internal`访问说明符允许一个类将其成员变量和成员函数暴露给当前`程序集`中的其他函数和对象。

### Protected Internal

&emsp;&emsp;`Protected Internal`访问修饰符允许在本类、派生类或者包含该类的`程序集`中访问。