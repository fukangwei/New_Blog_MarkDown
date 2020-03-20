---
title: CSharp常量
date: 2020-03-20 19:03:39
categories: C#
---
&emsp;&emsp;常量是固定值，程序执行期间不会改变。常量可以是任何基本数据类型，比如整数常量、浮点常量、字符常量或者字符串常量，还有枚举常量。<!--more-->
&emsp;&emsp;常量可以被当作常规的变量，只是它们的值在定义后不能被修改。

### 整数常量

&emsp;&emsp;整数常量可以是十进制、八进制或十六进制的常量。前缀指定基数：`0x`或`0X`表示十六进制，`0`表示八进制，没有前缀则表示十进制。
&emsp;&emsp;整数常量也可以有后缀，可以是`U`和`L`的组合。其中，`U`和`L`分别表示`unsigned`和`long`。后缀可以是大写或者小写，多个后缀以任意顺序进行组合。
&emsp;&emsp;这里有一些整数常量的实例：

``` cs
212    // 合法，十进制
215u   // 合法，无符号整型
0213   // 合法，八进制
0xFeeL // 合法，十六进制
30l    // 合法，long型
30ul   // 合法，无符号long型
078    // 非法：8 不是一个八进制数字
032UU  // 非法：不能重复后缀
```

### 浮点常量

&emsp;&emsp;一个浮点常量是由整数部分、小数点、小数部分和指数部分组成。你可以使用小数形式或者指数形式来表示浮点常量。

- 使用小数形式表示时，必须包含小数点、指数或同时包含两者。
- 使用指数形式表示时，必须包含整数部分、小数部分或同时包含两者。有符号的指数是用`e`或`E`表示的。

&emsp;&emsp;这里有一些浮点常量的实例：

``` cs
3.14159    // 合法
314159E-5L // 合法
510E       // 非法：不完全指数 */
210f       // 非法：没有小数或指数 */
.e55       // 非法：缺少整数或小数 */
```

### 字符常量

&emsp;&emsp;字符常量是括在单引号里，例如`'x'`，并且可以存储在一个简单的字符类型变量中。一个字符常量可以是一个普通字符(例如`'x'`)、一个转义序列(例如`'\t'`)或者一个通用字符(例如`'\u02C0'`)。
&emsp;&emsp;`C#`有一些特定的字符，它们的前面都带有反斜杠`\`，并且有特殊的意义，可用于表示换行符(`\n`)或制表符`tab`(`\t`)。在这里列出一些转义字符：

转义序列 | 含义  | 转义序列 | 含义
--------|-------|---------|-----
`\\`    | `\`   | `\a`    | `Alert`或`bell`
`\'`    | `'`   | `\b`    | `Backspace`
`\"`    | `"`   | `\?`    | `?`
`\f`    | 换页符 | `\v`    | 垂直制表符
`\r`    | 回车   | `\t`    | 水平制表符
`\n`    | 换行符

``` cs
using System;

namespace EscapeChar {
    class Program {
        static void Main(string[] args) {
            Console.WriteLine("Hello\tWorld\n\n");
        }
    }
}
```

### 字符串常量

&emsp;&emsp;字符串常量是括在双引号`""`里，或者是括在`@""`里。字符串常量包含的字符与字符常量相似，可以是普通字符、转义序列和通用字符。
&emsp;&emsp;使用字符串常量时，可以把一个很长的行拆成多个行，可以使用空格分隔各个部分。
&emsp;&emsp;这里是一些字符串常量的实例：

``` cs
string a = "hello, world";                // hello, world
string b = @"hello, world";               // hello, world
string c = "hello \t world";              // hello world
string d = @"hello \t world";             // hello \t world
string e = "Joe said \"Hello\" to me";    // Joe said "Hello" to me
string f = @"Joe said ""Hello"" to me";   // Joe said "Hello" to me
string g = "\\\\server\\share\\file.txt"; // \\server\share\file.txt
string h = @"\\server\share\file.txt";    // \\server\share\file.txt
string i = "one\r\ntwo\r\nthree";         // i和j是相同的
string j = @"one
two
three";
```

### 定义常量

&emsp;&emsp;常量是使用`const`关键字来定义的：

``` cs
const <data_type> <constant_name> = value;
```

代码示例如下：

``` cs
using System;

public class ConstTest {
    class SampleClass {
        public int x;
        public int y;
        public const int c1 = 5;
        public const int c2 = c1 + 5;

        public SampleClass(int p1, int p2) {
            x = p1;
            y = p2;
        }
    }

    static void Main() {
        SampleClass mC = new SampleClass(11, 22);
        Console.WriteLine("x = {0}, y = {1}", mC.x, mC.y);
        Console.WriteLine("c1 = {0}, c2 = {1}", SampleClass.c1, SampleClass.c2);
    }
}
```

执行结果：

``` cs
x = 11, y = 22
c1 = 5, c2 = 10
```