---
title: struct模块
date: 2018-12-27 20:00:50
categories: Python语法
---
&emsp;&emsp;当`Python`需要和二进制数据打交道时，就要用到`struct`模块了。`struct`模块为`Python`与`C`的混合编程、处理二进制文件以及网络协议通信提供了便利。`struct`模块主要用到三个函数：

``` python
struct.pack(fmt, v1, v2, ...)
struct.unpack(fmt, string)
struct.calcsize(fmt)
```

函数`pack`负责将不同的变量打包在一起，成为一个字节字符串，即类似于`C`语言中的字节流；函数`unpack`将字节字符串解包成为变量；函数`calsize`计算按照格式`fmt`打包的结果有多少个字节。打包格式`fmt`确定了将变量按照什么方式打包成字节流，其包含了一系列的格式字符串：

Format | C Type | Python type | Standard size
-------|--------|-------------|--------------
`x` | `pad byte`           | `no value`         |
`c` | `char`               | `string of length` | `1`
`b` | `signed char`        | `integer`          | `1`
`B` | `unsigned char`      | `integer`          | `1`
`?` | `_Bool`              | `bool`             | `1`
`h` | `short`              | `integer`          | `2`
`H` | `unsigned short`     | `integer`          | `2`
`i` | `int`                | `integer`          | `4`
`I` | `unsigned int`       | `integer`          | `4`
`l` | `long`               | `integer`          | `4`
`L` | `unsigned long`      | `integer`          | `4`
`q` | `long long`          | `integer`          | `8`
`Q` | `unsigned long long` | `integer`          | `8`
`f` | `float`              | `float`            | `4`
`d` | `double`             | `float`            | `8`
`s` | `char[]`             | `string`           |
`p` | `char[]`             | `string`           |
`P` | `void *`             | `integer`          |

有时还需要用到一些符号：

Character | Byte order | Size | Alignment
----------|------------|------|----------
`@` | `native`                 | `native`   | `native`
`=` | `native`                 | `standard` | `none`
`<` | `little-endian`          | `standard` | `none`
`>` | `big-endian`             | `standard` | `none`
`!` | `network (= big-endian)` | `standard` | `none`

### 使用struct打包定长结构

&emsp;&emsp;一般情况下，使用`struct`打包的数据都是定长的。定长的数据代表你需要明确地给出要打包或者解包的数据长度，否则打包解包函数将会出错：

``` python
import struct
​
a = struct.pack("2I3sI", 12, 34, b"abc", 56)
b = struct.unpack("2I3sI", a)
print(b)  # 结果为(12, 34, b'abc', 56)
```

上面的代码将两个整数`12`和`34`、字符串`abc`和整数`56`一起打包成为一个字节字符流，然后再解包。打包格式中明确地指出了打包的长度：`2I`表明起始是两个`unsigned int`，`3s`表明长度为`3`的字符串(对`C`语言而言长度为`4`)，最后一个`I`表示最后紧跟一个`unsigned int`。
&emsp;&emsp;我们可以调用`calcsize`来计算`2I3sI`这个模式占用的字节数：

``` python
print(struct.calcsize("2I3sI"))  # 输出“16”
```

可以看到，上面的三个整型加一个`3`字符的字符串一共占用了`16`个字节。为什么会是`16`个字节，不应该是`15`个字节吗？其实在`struct`的打包过程中，根据特定类型的要求，必须进行字节对齐。由于默认`unsigned int`型占用`4`个字节，因此要在字符串的位置进行`4`字节对齐，因此即使是`3`个字符的字符串也要占用`4`个字节。
&emsp;&emsp;使用特殊符号的示例：

``` python
import struct
​
# “>”表示字节顺序是“big-endian”，f表示浮点类型
a = struct.pack('>f', float(3.1415))
b = struct.unpack('>f', a)
print(b)  # 输出(3.1414999961853027,)
```