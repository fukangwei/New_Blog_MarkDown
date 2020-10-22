---
title: CSharp运算符重载
date: 2020-03-21 13:45:09
categories: C#
---
&emsp;&emsp;你可以重定义或重载`C#`中内置的运算符。因此，程序员也可以使用用户自定义类型的运算符。<!--more-->
&emsp;&emsp;重载运算符是具有特殊名称的函数，是通过关键字`operator`后跟运算符的符号来定义的。与其他函数一样，重载运算符有返回类型和参数列表。

``` cs
public static Box operator+ (Box b, Box c) {
    Box box = new Box();
    box.length = b.length + c.length;
    box.breadth = b.breadth + c.breadth;
    box.height = b.height + c.height;
    return box;
}
```

上面的函数为用户自定义的类`Box`实现了加法运算符`+`。它把两个`Box`对象的属性相加，并返回相加后的`Box`对象。

### 运算符重载的实现

&emsp;&emsp;代码示例如下：

``` cs
using System;

namespace OperatorOvlApplication {
    class Box {
        private double length;
        private double breadth;
        private double height;

        public double getVolume() {
            return length * breadth * height;
        }

        public void setLength(double len) {
            length = len;
        }

        public void setBreadth(double bre) {
            breadth = bre;
        }

        public void setHeight(double hei) {
            height = hei;
        }

        public static Box operator +(Box b, Box c) {
            Box box = new Box();
            box.length = b.length + c.length;
            box.breadth = b.breadth + c.breadth;
            box.height = b.height + c.height;
            return box;
        }
    }

    class Tester {
        static void Main(string[] args) {
            Box Box1 = new Box();
            Box Box2 = new Box();
            Box Box3 = new Box();
            double volume = 0.0;

            Box1.setLength(6.0);
            Box1.setBreadth(7.0);
            Box1.setHeight(5.0);

            Box2.setLength(12.0);
            Box2.setBreadth(13.0);
            Box2.setHeight(10.0);

            volume = Box1.getVolume();
            Console.WriteLine("Box1的体积：{0}", volume);

            volume = Box2.getVolume();
            Console.WriteLine("Box2的体积：{0}", volume);

            Box3 = Box1 + Box2;

            volume = Box3.getVolume();
            Console.WriteLine("Box3的体积：{0}", volume);
        }
    }
}
```

执行结果：

``` cs
Box1的体积：210
Box2的体积：1560
Box3的体积：5400
```

### 可重载和不可重载运算符

&emsp;&emsp;下表描述了`C#`中运算符重载的能力：

运算符                                               | 描述
-----------------------------------------------------|-------
`+`、`-`、`!`、`~`、`++`、`--`                        | 这些一元运算符只有一个操作数，且可以被重载
`+`、`-`、`*`、`/`、`%`                               | 这些二元运算符带有两个操作数，且可以被重载
`==`、`!=`、`<`、`>`、`<=`、`>=`                      | 这些比较运算符可以被重载
`&&`、<code>&#124;&#124;</code>                      | 这些条件逻辑运算符不能被直接重载
`+=`、`-=`、`*=`、`/=`、`%=`                          | 这些赋值运算符不能被重载
`=`、`.`、`?:`、`->`、`new`、`is`、`sizeof`、`typeof` | 这些运算符不能被重载

``` cs
using System;

namespace OperatorOvlApplication {
    class Box {
        private double length;
        private double breadth;
        private double height;

        public double getVolume() {
            return length * breadth * height;
        }

        public void setLength(double len) {
            length = len;
        }

        public void setBreadth(double bre) {
            breadth = bre;
        }

        public void setHeight(double hei) {
            height = hei;
        }

        public static Box operator +(Box b, Box c) {
            Box box = new Box();
            box.length = b.length + c.length;
            box.breadth = b.breadth + c.breadth;
            box.height = b.height + c.height;
            return box;
        }

        public static bool operator ==(Box lhs, Box rhs) {
            bool status = false;

            if (lhs.length == rhs.length &&
                lhs.height == rhs.height &&
                lhs.breadth == rhs.breadth) {
                status = true;
            }

            return status;
        }

        public static bool operator !=(Box lhs, Box rhs) {
            bool status = false;

            if (lhs.length != rhs.length ||
                lhs.height != rhs.height ||
                lhs.breadth != rhs.breadth) {
                status = true;
            }

            return status;
        }
    }

    class Tester {
        static void Main(string[] args) {
            Box Box1 = new Box();
            Box Box2 = new Box();
            Box Box3 = new Box();
            Box Box4 = new Box();
            double volume = 0.0;

            Box1.setLength(6.0);
            Box1.setBreadth(7.0);
            Box1.setHeight(5.0);

            Box2.setLength(12.0);
            Box2.setBreadth(13.0);
            Box2.setHeight(10.0);

            Box3 = Box1 + Box2;
            volume = Box3.getVolume();
            Console.WriteLine("Box3 的体积：{0}", volume);

            Box4 = Box3;

            if (Box3 == Box4)
                Console.WriteLine("Box3 等于 Box4");
            else
                Console.WriteLine("Box3 不等于 Box4");
        }
    }
}
```

执行结果：

``` cs
Box3 的体积：5400
Box3 等于 Box4
```