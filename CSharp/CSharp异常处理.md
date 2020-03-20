---
title: CSharp异常处理
date: 2020-03-21 20:46:36
categories: C#
---
&emsp;&emsp;异常是在程序执行期间出现的问题。`C#`中的异常是对程序运行时出现的特殊情况的一种响应，比如尝试除以`0`。<!--more-->
&emsp;&emsp;异常提供了一种把程序控制权从某个部分转移到另一个部分的方式。`C#`异常处理时建立在四个关键词之上的：`try`、`catch`、`finally`和`throw`。

- `try`：一个`try`块标识了一个将被激活的特定的异常的代码块。后跟一个或多个`catch`块。
- `catch`：程序通过异常处理程序捕获异常。`catch`关键字表示异常的捕获。
- `finally`：`finally`块用于执行给定的语句，不管异常是否被抛出都会执行。例如，如果你打开一个文件，不管是否出现异常文件都要被关闭。
- `throw`：用于抛出一个异常。

### 语法

&emsp;&emsp;假设一个块将出现异常，可以使用`try`和`catch`关键字捕获异常，`try/catch`块内的代码为受保护的代码：

``` cs
try {
    // 引起异常的语句
} catch (ExceptionName e1) {
    // 错误处理代码
} catch (ExceptionName e2) {
    // 错误处理代码
} catch (ExceptionName eN) {
    // 错误处理代码
} finally {
    // 要执行的语句
}
```

你可以列出多个`catch`语句捕获不同类型的异常，以防`try`块在不同的情况下生成多个异常。

### 异常类

&emsp;&emsp;`C#`中的异常类主要是直接或间接地派生于`System.Exception`。`System.ApplicationException`和`System.SystemException`是派生于`System.Exception`的异常类。

- `System.ApplicationException`：类支持由应用程序生成的异常，所以程序员定义的异常都应派生自该类。
- `System.SystemException`：类是所有预定义的系统异常的基类。

&emsp;&emsp;下表列出了一些派生自`Sytem.SystemException`的预定义的异常类：

异常类                               | 描述
------------------------------------|------
`System.IO.IOException`             | 处理`I/O`错误
`System.IndexOutOfRangeException`   | 处理当方法指向超出范围的数组索引时生成的错误
`System.ArrayTypeMismatchException` | 处理当数组类型不匹配时生成的错误
`System.NullReferenceException`     | 处理当依从一个空对象时生成的错误
`System.DivideByZeroException`      | 处理当除以零时生成的错误
`System.InvalidCastException`       | 处理在类型转换期间生成的错误
`System.OutOfMemoryException`       | 处理空闲内存不足生成的错误
`System.StackOverflowException`     | 处理栈溢出生成的错误

### 异常处理

&emsp;&emsp;`C#`以`try`和`catch`块的形式提供了一种结构化的异常处理方案。使用这些块，把核心程序语句与错误处理语句分离开。
&emsp;&emsp;这些错误处理块是使用`try`、`catch`和`finally`关键字实现的。下面是一个当除以零时抛出异常的实例：

``` cs
using System;

namespace ErrorHandlingApplication {
    class DivNumbers {
        int result;

        DivNumbers() {
            result = 0;
        }

        public void division(int num1, int num2) {
            try {
                result = num1 / num2;
            } catch (DivideByZeroException e) {
                Console.WriteLine("Exception caught: {0}", e);
            } finally {
                Console.WriteLine("Result: {0}", result);
            }
        }

        static void Main(string[] args) {
            DivNumbers d = new DivNumbers();
            d.division(25, 0);
        }
    }
}
```

执行结果：

``` cs
Exception caught: System.DivideByZeroException: Attempted to divide by zero.
...
```

### 创建用户自定义异常

&emsp;&emsp;你也可以定义自己的异常，用户自定义的异常类是派生于`ApplicationException`。

``` cs
using System;

namespace UserDefinedException {
    class TestTemperature {
        static void Main(string[] args) {
            Temperature temp = new Temperature();

            try {
                temp.showTemp();
            } catch (TempIsZeroException e) {
                Console.WriteLine("TempIsZeroException: {0}", e.Message);
            }
        }
    }
}

public class TempIsZeroException : ApplicationException {
    public TempIsZeroException(string message) : base(message) {
    }
}

public class Temperature {
    int temperature = 0;

    public void showTemp() {
        if (temperature == 0) {
            throw (new TempIsZeroException("Zero Temperature found"));
        } else {
            Console.WriteLine("Temperature: {0}", temperature);
        }
    }
}
```

执行结果：

``` cs
TempIsZeroException: Zero Temperature found
```

### 抛出对象

&emsp;&emsp;如果异常是直接或间接派生于`System.Exception`，你可以抛出一个对象。你可以在`catch`块中使用`throw`语句来抛出当前的对象：

``` cs
Catch(Exception e) {
   ...
   Throw e
}
```