---
title: CSharp类
date: 2020-03-21 11:46:37
categories: C#
---
### 类的定义

&emsp;&emsp;类的完整定义如下：<!--more-->

``` cs
<access specifier> class  class_name {
    /* member variables */
    <access specifier> <data type> variable1;
    <access specifier> <data type> variable2;
    ...
    <access specifier> <data type> variableN;

    /* member methods */
    <access specifier> <return type> method1 (parameter_list) {
        /* method body */
    }

    <access specifier> <return type> method2 (parameter_list) {
        /* method body */
    }
    ...
    <access specifier> <return type> methodN (parameter_list) {
        /* method body */
    }
}
```

- 访问标识符`<access specifier>`指定了对类及其成员的访问规则。如果没有指定，则使用默认的访问标识符。类的默认访问标识符是`internal`，成员的默认访问标识符是`private`。
- 数据类型`<data type>`指定了变量的类型，返回类型`<return type>`指定了方法返回的数据类型。
- 如果要访问类的成员，你要使用点运算符`.`。

``` cs
using System;

namespace BoxApplication {
    class Box {
        public double length;
        public double breadth;
        public double height;
    }

    class Boxtester {
        static void Main(string[] args) {
            Box Box1 = new Box();
            double volume = 0.0;

            Box1.height = 5.0;
            Box1.length = 6.0;
            Box1.breadth = 7.0;

            volume = Box1.height * Box1.length * Box1.breadth;
            Console.WriteLine("Box1的体积：{0}", volume);
        }
    }
}
```

执行结果：

``` cs
Box1的体积：210
```

### 成员函数和封装

&emsp;&emsp;类的成员函数是一个在类定义中有它的定义或原型的函数，就像其他变量一样。作为类的一个成员，它能在类的任何对象上操作，且能访问该对象的类的所有成员。

``` cs
using System;

namespace BoxApplication {
    class Box {
        private double length;
        private double breadth;
        private double height;

        public void setLength(double len) {
            length = len;
        }

        public void setBreadth(double bre) {
            breadth = bre;
        }

        public void setHeight(double hei) {
            height = hei;
        }

        public double getVolume() {
            return length * breadth * height;
        }
    }

    class Boxtester {
        static void Main(string[] args) {
            Box Box1 = new Box();
            double volume;

            Box1.setLength(6.0);
            Box1.setBreadth(7.0);
            Box1.setHeight(5.0);

            volume = Box1.getVolume();
            Console.WriteLine("Box1的体积：{0}", volume);
        }
    }
}
```

执行结果：

``` cs
Box1的体积：210
```

### 构造函数

&emsp;&emsp;类的`构造函数`是类的一个特殊的成员函数，当创建类的新对象时执行。构造函数的名称与类的名称完全相同，它没有任何返回类型。

``` cs
using System;

namespace LineApplication {
    class Line {
        private double length;

        public Line() {
            Console.WriteLine("对象已创建");
        }

        public void setLength(double len) {
            length = len;
        }

        public double getLength() {
            return length;
        }

        static void Main(string[] args) {
            Line line = new Line();
            line.setLength(6.0);
            Console.WriteLine("线条的长度：{0}", line.getLength());
        }
    }
}
```

执行结果：

``` cs
对象已创建
线条的长度：6
```

&emsp;&emsp;默认的构造函数没有任何参数，但是可以自定义一个带有参数的构造函数，这种构造函数叫做`参数化构造函数`。这种技术可以帮助你在创建对象的同时给对象赋初始值：

``` cs
using System;

namespace LineApplication {
    class Line {
        private double length;

        public Line(double len) {
            Console.WriteLine("对象已创建，length = {0}", len);
            length = len;
        }

        public void setLength(double len) {
            length = len;
        }

        public double getLength() {
            return length;
        }

        static void Main(string[] args) {
            Line line = new Line(10.0);
            Console.WriteLine("线条的长度：{0}", line.getLength());
            line.setLength(6.0);
            Console.WriteLine("线条的长度：{0}", line.getLength());
        }
    }
}
```

执行结果：

``` cs
对象已创建，length = 10
线条的长度：10
线条的长度：6
```

### 析构函数

&emsp;&emsp;类的`析构函数`是类的一个特殊的成员函数，当类的对象被销毁时执行。
&emsp;&emsp;析构函数的名称是在类的名称前加上一个波浪形`~`作为前缀，它不返回值，也不带任何参数。
&emsp;&emsp;析构函数用于在结束程序之前释放资源(例如关闭文件、释放内存)。析构函数不能继承或重载。

``` cs
using System;

namespace LineApplication {
    class Line {
        private double length;

        public Line() {
            Console.WriteLine("对象已创建");
        }

        ~Line() { /* 析构函数 */
            Console.WriteLine("对象已删除");
        }

        public void setLength(double len) {
            length = len;
        }

        public double getLength() {
            return length;
        }

        static void Main(string[] args) {
            Line line = new Line();
            line.setLength(6.0);
            Console.WriteLine("线条的长度：{0}", line.getLength());
        }
    }
}
```

执行结果：

``` cs
对象已创建
线条的长度：6
对象已删除
```

### 静态成员

&emsp;&emsp;可以使用`static`关键字把类成员定义为静态的。当我们声明一个类成员为静态时，意味着无论有多少个类的对象被创建，只会有一个该静态成员的副本。
&emsp;&emsp;关键字`static`意味着类中只有一个该成员的实例。静态变量用于定义常量，因为它们的值可以通过直接调用类而不需要创建类的实例来获取。静态变量可在成员函数或类的定义外部进行初始化。你也可以在类的定义内部初始化静态变量。

``` cs
using System;

namespace StaticVarApplication {
    class StaticVar {
       public static int num;

        public void count() {
            num++;
        }

        public int getNum() {
            return num;
        }
    }

    class StaticTester {
        static void Main(string[] args) {
            StaticVar s1 = new StaticVar();
            StaticVar s2 = new StaticVar();
            s1.count();
            s1.count();
            s2.count();
            Console.WriteLine("s1的变量num：{0}", s1.getNum());
            Console.WriteLine("s2的变量num：{0}", s2.getNum());
        }
    }
}
```

执行结果：

``` cs
s1的变量num：3
s2的变量num：3
```

&emsp;&emsp;你也可以把一个成员函数声明为`static`，这样的函数只能访问静态变量。静态函数在对象被创建之前就已经存在。

``` cs
using System;

namespace StaticVarApplication {
    class StaticVar {
        public static int num;

        public void count() {
            num++;
        }

        public static int getNum() {
            return num;
        }
    }

    class StaticTester {
        static void Main(string[] args) {
            StaticVar s = new StaticVar();
            s.count();
            s.count();
            Console.WriteLine("变量num：{0}", StaticVar.getNum());
        }
    }
}
```

执行结果：

``` cs
变量num：2
```