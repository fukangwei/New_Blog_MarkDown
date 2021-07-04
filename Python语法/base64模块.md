---
title: base64模块
categories: Python语法
date: 2019-02-11 11:34:05
---
### 原理

&emsp;&emsp;`Base64`是一种用`64`个字符来表示任意二进制数据的方法。<!--more-->
&emsp;&emsp;`Base64`编码的步骤如下：

1. 将需要编码的数据拆分成字节数组，以`3`个字节为一组，按顺序排列为`24`位数据。
2. 把这`24`位数据分成`4`组，即每组`6`位数据。
3. 在每组数据的最高位前补两个`0`，凑足一个字节。这样就把以`3`字节为一组的数据重新编码成了`4`个字节。
4. 根据`base64`索引表，将这`4`个字节二进制数据替换为对应的字符。
5. 如果最后一组数据不够`3`个字节，则填充`1`至`2`个`0`，并在编码完成后，在结尾处添加`1`至`2`个`=`。

### base64

&emsp;&emsp;`base64`模块可以直接进行`base64`的编解码：

``` python
>>> import base64
>>> base64.b64encode(b'binary\x00string')
b'YmluYXJ5AHN0cmluZw=='
>>> base64.b64decode(b'YmluYXJ5AHN0cmluZw==')
b'binary\x00string'
```

&emsp;&emsp;由于标准的`Base64`编码可能出现`+`和`/`，在`URL`中就不能直接作为参数，所以又有一种`url safe`的`base64`编码：

``` python
>>> base64.b64encode(b'i\xb7\x1d\xfb\xef\xff')
b'abcd++//'
>>> base64.urlsafe_b64encode(b'i\xb7\x1d\xfb\xef\xff')
b'abcd--__'
>>> base64.urlsafe_b64decode(b'abcd--__')
b'i\xb7\x1d\xfb\xef\xff'
```