---
title: CSharp接口
date: 2020-03-21 14:19:05
categories: C#
---
&emsp;&emsp;接口定义了所有类继承接口时应遵循的语法合同。接口定义了语法合同`是什么`部分，派生类定义了语法合同`怎么做`部分。<!--more-->
&emsp;&emsp;接口定义了属性、方法和事件，这些都是接口的成员。接口只包含了成员的声明，成员的定义是派生类的责任。

### 定义接口

&emsp;&emsp;接口使用`interface`关键字声明，它与类的声明类似。接口声明默认是`public`的。下面是一个接口声明的实例：

``` cs
interface IMyInterface {
    void MethodToImplement();
}
```

以上代码定义了接口`IMyInterface`，通常接口命令以`I`字母开头。这个接口只有一个方法`MethodToImplement`，没有参数和返回值，当然我们可以按照需求设置参数和返回值。
&emsp;&emsp;完整的代码如下：

``` cs
using System;

interface IMyInterface {
    void MethodToImplement(); /* 接口成员 */
}

class InterfaceImplementer : IMyInterface {
    public void MethodToImplement() {
        Console.WriteLine("MethodToImplement() called.");
    }

    static void Main() {
        InterfaceImplementer iImp = new InterfaceImplementer();
        iImp.MethodToImplement();
    }
}
```

执行结果：

``` cs
MethodToImplement() called.
```

### 接口继承

&emsp;&emsp;以下实例定义了两个接口`IMyInterface`和`IParentInterface`。如果一个接口继承其他接口，那么实现类或结构就需要实现所有接口的成员。
&emsp;&emsp;以下实例`IMyInterface`继承了`IParentInterface`接口，因此接口实现类必须实现`MethodToImplement`和`ParentInterfaceMethod`方法：

``` cs
using System;

interface IParentInterface {
    void ParentInterfaceMethod();
}

interface IMyInterface : IParentInterface {
    void MethodToImplement();
}

class InterfaceImplementer : IMyInterface {
    public void MethodToImplement() {
        Console.WriteLine("MethodToImplement() called.");
    }

    public void ParentInterfaceMethod() {
        Console.WriteLine("ParentInterfaceMethod() called.");
    }

    static void Main() {
        InterfaceImplementer iImp = new InterfaceImplementer();
        iImp.MethodToImplement();
        iImp.ParentInterfaceMethod();
    }
}
```

执行结果：

``` cs
MethodToImplement() called.
ParentInterfaceMethod() called.
```