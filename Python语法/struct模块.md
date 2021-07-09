---
title: struct模块
categories: Python语法
date: 2018-12-27 20:00:50
---
### 基础用法

&emsp;&emsp;当`Python`需要和二进制数据打交道时，就要用到`struct`模块。`struct`模块主要有`3`个函数：<!--more-->

``` python
struct.pack(fmt, v1, v2, ...)
struct.unpack(fmt, string)
struct.calcsize(fmt)
```

- `pack`负责将不同的变量打包在一起，成为一个字节字符串，类似于`C`语言中的字节流。
- `unpack`将字节字符串解包成为变量。
- `calsize`计算按照格式`fmt`打包的结果有多少个字节。

&emsp;&emsp;`fmt`确定了将变量按照什么方式打包成字节流：

Format | C Type               | Python type | Standard size | Format | C Type          | Python type        | Standard size
-------|----------------------|-------------|---------------|--------|-----------------|--------------------|---------------
`x`    | `pad byte`           | `no value`  |               | `c`    | `char`          | `string of length` | `1`
`b`    | `signed char`        | `integer`   | `1`           | `B`    | `unsigned char` | `integer`          | `1`
`?`    | `_Bool`              | `bool`      | `1`           | `h`    | `short`         | `integer`          | `2`
`H`    | `unsigned short`     | `integer`   | `2`           | `i`    | `int`           | `integer`          | `4`
`I`    | `unsigned int`       | `integer`   | `4`           | `l`    | `long`          | `integer`          | `4`
`L`    | `unsigned long`      | `integer`   | `4`           | `q`    | `long long`     | `integer`          | `8`
`Q`    | `unsigned long long` | `integer`   | `8`           | `f`    | `float`         | `float`            | `4`
`d`    | `double`             | `float`     | `8`           | `s`    | `char[]`        | `string`           |
`p`    | `char[]`             | `string`    |               | `P`    | `void *`        | `integer`          |

&emsp;&emsp;有时还需要用到一些符号：

Character | Byte order               | Size       | Alignment | Character | Byte order   | Size       | Alignment
----------|--------------------------|------------|-----------|-----------|--------------|------------|-----------
`@`       | `native`                 | `native`   | `native`  | `=`       | `native`     | `standard` | `none`
`<`       | `little-endian`          | `standard` | `none`    | `>`       | `big-endian` | `standard` | `none`
`!`       | `network (= big-endian)` | `standard` | `none`

### 定长结构

&emsp;&emsp;如下代码将两个整数`12`和`34`、字符串`abc`和整数`56`一起打包成为一个字节字符流，然后再解包：

``` python
import struct

a = struct.pack("2I3sI", 12, 34, b"abc", 56)
b = struct.unpack("2I3sI", a)
print(b)  # 结果为(12, 34, b'abc', 56)
```

打包格式明确地指出了打包的长度：

1. `2I`表明起始是两个`unsigned int`。
2. `3s`表明长度为`3`的字符串。
3. 最后一个`I`表示后面紧跟一个`unsigned int`。

&emsp;&emsp;可以调用`calcsize`来计算`2I3sI`这个模式占用的字节数：

``` python
print(struct.calcsize("2I3sI"))  # 输出“16”
```

### 特殊符号

&emsp;&emsp;使用特殊符号的示例：

``` python
import struct

# “>”表示字节顺序是“big-endian”，f表示浮点类型
a = struct.pack('>f', float(3.1415))
b = struct.unpack('>f', a)
print(b)  # 输出(3.1414999961853027,)
```