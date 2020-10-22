---
title: CSharp命名空间
date: 2020-03-21 15:50:34
categories: C#
---
&emsp;&emsp;命名空间的设计目的是提供一种让一组名称与其他名称分隔开的方式。在一个命名空间中，声明的类的名称与另一个命名空间中声明的相同的类的名称不冲突。<!--more-->
&emsp;&emsp;举一个计算机系统中的例子，一个文件夹(或目录)中可以包含多个文件夹，每个文件夹中不能有相同的文件名，但不同文件夹中的文件可以重名。

### 定义命名空间

&emsp;&emsp;命名空间的定义是以关键字`namespace`开始，后跟命名空间的名称：

``` cs
namespace namespace_name {
    /* 代码声明 */
}
```

为了调用支持命名空间版本的函数或变量，会把命名空间的名称置于前面：

``` cs
namespace_name.item_name;
```

下面的程序演示了命名空间的用法：

``` cs
using System;

namespace first_space {
    class namespace_cl {
        public void func() {
            Console.WriteLine("Inside first_space");
        }
    }
}

namespace second_space {
    class namespace_cl {
        public void func() {
            Console.WriteLine("Inside second_space");
        }
    }
}

class TestClass {
    static void Main(string[] args) {
        first_space.namespace_cl fc = new first_space.namespace_cl();
        second_space.namespace_cl sc = new second_space.namespace_cl();
        fc.func();
        sc.func();
    }
}
```

执行结果：

``` cs
Inside first_space
Inside second_space
```

### using

&emsp;&emsp;`using`关键字表明程序使用的是给定命名空间中的名称。例如，我们在程序中使用`System`命名空间，其中定义了类`Console`，可以写成：

``` cs
Console.WriteLine ("Hello there");
```

也可以写成：

``` cs
System.Console.WriteLine("Hello there");
```

&emsp;&emsp;代码示例如下：

``` cs
using System;
using first_space;
using second_space;

namespace first_space {
    class abc {
        public void func() {
            Console.WriteLine("Inside first_space");
        }
    }
}

namespace second_space {
    class efg {
        public void func() {
            Console.WriteLine("Inside second_space");
        }
    }
}

class TestClass {
    static void Main(string[] args) {
        abc fc = new abc();
        efg sc = new efg();
        fc.func();
        sc.func();
    }
}
```

执行结果：

``` cs
Inside first_space
Inside second_space
```

### 嵌套命名空间

&emsp;&emsp;命名空间可以被嵌套，即你可以在一个命名空间内定义另一个命名空间：

``` cs
namespace namespace_name1 {
    /* 代码声明 */
    namespace namespace_name2 {
        /* 代码声明 */
    }
}
```

你可以使用点运算符`.`访问嵌套的命名空间的成员：

``` cs
using System;

namespace SomeNameSpace {
    public class MyClass {
        static void Main() {
            Console.WriteLine("In SomeNameSpace");
            Nested.NestedNameSpaceClass.SayHello();
        }
    }

    namespace Nested { /* 嵌套命名空间 */
        public class NestedNameSpaceClass {
            public static void SayHello() {
                Console.WriteLine("In Nested");
            }
        }
    }
}
```

执行结果：

``` cs
In SomeNameSpace
In Nested
```