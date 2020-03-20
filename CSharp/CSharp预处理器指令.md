---
title: CSharp预处理器指令
date: 2020-03-21 16:05:46
categories: C#
---
&emsp;&emsp;预处理器指令指导编译器在实际编译开始之前对信息进行预处理。<!--more-->
&emsp;&emsp;在`C#`中，预处理器指令用于代码的条件编译，与`C`和`C++`不同的是，它们不是用来创建宏。

### C#预处理器指令列表

&emsp;&emsp;下表列出了`C#`中可用的预处理器指令：

预处理器指令 | 描述
------------|------
`#define`   | 它用于定义一系列成为符号的字符
`#undef`    | 它用于取消定义符号
`#if`       | 它用于测试符号是否为真
`#else`     | 它用于创建复合条件指令，与`#if`一起使用
`#elif`     | 它用于创建复合条件指令
`#endif`    | 指定一个条件指令的结束
`#line`     | 它可以让你修改编译器的行数以及输出错误和警告的文件名
`#error`    | 它允许从代码的指定位置生成一个错误
`#warning`  | 它允许从代码的指定位置生成一个警告

### \#define预处理器

&emsp;&emsp;`#define`预处理器指令用于创建符号常量。其语法如下：

``` cs
#define symbol
```

代码示例如下：

``` cs
#define PI
using System;

namespace PreprocessorDAppl {
    class Program {
        static void Main(string[] args) {
#if (PI)
            Console.WriteLine("PI is defined");
#else
            Console.WriteLine("PI is not defined");
#endif
        }
    }
}
```

执行结果：

``` cs
PI is defined
```

### 条件指令

&emsp;&emsp;你可以使用`#if`指令来创建一个条件指令。条件指令用于测试符号是否为真。如果为真，编译器会执行`#if`和下一个指令之间的代码。
&emsp;&emsp;条件指令的语法如下，`symbol`是要测试的符号名称：

``` cs
#if symbol [operator symbol]
```

&emsp;&emsp;常见运算符如下：

运算符                     | 含义
--------------------------|----
`==`                      | 等于
`!=`                      | 不等于
`&&`                      | 与
<code>&#124;&#124;</code> | 或

一个以`#if`指令开始的条件指令，必须显式地以一个`#endif`指令终止：

``` cs
#define DEBUG
#define VC_V10
using System;

public class TestClass {
    public static void Main() {
#if (DEBUG && !VC_V10)
        Console.WriteLine("DEBUG is defined");
#elif (!DEBUG && VC_V10)
        Console.WriteLine("VC_V10 is defined");
#elif (DEBUG && VC_V10)
        Console.WriteLine("DEBUG and VC_V10 are defined");
#else
        Console.WriteLine("DEBUG and VC_V10 are not defined");
#endif
    }
}
```

执行结果：

``` cs
DEBUG and VC_V10 are defined
```

### \#warning和\#error

&emsp;&emsp;当编译器遇到它们时，会分别产生`警告`或`错误`：

1. 如果编译器遇到`#warning`指令，会给用户显示`#warning`指令后面的文本，之后编译继续进行。
2. 如果编译器遇到`#error`指令，就会给用户显示后面的文本，作为一条编译错误消息，然后会立即退出编译。

``` cs
#if DEBUG && RELEASE
    #error "You've defined DEBUG and RELEASE simultaneously!"
#endif

#warning "Don't forget to remove this line before the boss tests the code!"
Console.WriteLine("I love this job");
```