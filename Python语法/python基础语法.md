---
title: python基础语法
categories: Python语法
date: 2019-01-10 08:54:28
---
### 编码

&emsp;&emsp;默认情况下，`Python 3`源码文件以`UTF-8`编码保存，所有的字符串都是`unicode`字符串。<!--more-->
&emsp;&emsp;当然，你也可以为源码文件指定不同的编码：

``` python
# -*- coding: cp-1252 -*-
```

### 标识符

- 第一个字符必须是英文字母表中的`字母`或`_`。
- 标识符的其他的部分可以是`字母`、`数字`或`_`。
- 标识符对大小写敏感。
- 在`Python 3`中，非`ASCII`标识符也是允许的。

### python保留字

&emsp;&emsp;`Python`提供的关键字如下：

``` python
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del',
'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda',
'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

### 注释

&emsp;&emsp;`Python`的单行注释以`#`开头：

``` python
# 第一个注释
print("Hello, Python!")  # 第二个注释
```

&emsp;&emsp;多行注释可以用多个`#`号：

``` python
# 第一个注释
# 第二个注释
print("Hello, Python!")
```

&emsp;&emsp;也可以使用`''' '''`或`""" """`的格式书写较长的注释：

``` python
def example(anything):
    ''' 形参为任意类型的对象，这个函数会将其原样返回 '''
    return anything
```

### 行与缩进

&emsp;&emsp;`python`使用缩进来表示代码块，缩进的空格数不是固定的。但是，属于同一个代码块的语句必须使用相同的缩进空格数：

``` python
if True:
    print("True")
else:
    print("False")
```

&emsp;&emsp;以下代码最后一行语句缩进空格数不一致，会导致运行错误：

``` python
if True:
    print("Answer")
    print("True")
else:
    print("Answer")
  print("False")  # 缩进不一致，会导致运行错误
```

### 多行语句

&emsp;&emsp;可以使用反斜杠`\`来实现多行语句：

``` python
total = item_one + \
        item_two + \
        item_three
```

&emsp;&emsp;在`[]`、`{}`或`()`中的多行语句，不需要使用反斜杠`\`：

``` python
total = ['item_one', 'item_two', 'item_three',
         'item_four', 'item_five']
```

### 等待用户输入

&emsp;&emsp;下面的程序显示`按下enter键后退出`后，会等待用户输入：

``` python
input("按下enter键后退出")
```

### 同一行显示多条语句

&emsp;&emsp;`Python`可以在同一行中使用多条语句，语句之间使用`;`分割：

``` python
import sys; x = 'runoob'; sys.stdout.write(x + '\n')
```

### 多个语句构成代码组

&emsp;&emsp;缩进相同的一组语句构成一个代码块，称之为`代码组`：

``` python
if expression:
    suite
elif expression:
    suite
else:
    suite
```

### Print输出

&emsp;&emsp;`print`默认输出是换行的。如果要实现不换行，需要在`print`中加上`end=" "`：

``` python
x = "a"
y = "b"
# 换行输出
print(x)
print(y)
print('---------')
# 不换行输出
print(x, end=" ")
print(y, end=" ")
```

执行结果：

``` python
a
b
---------
a b
```

### 类型

&emsp;&emsp;`python`的变量不需要声明类型，变量的类型由赋予它的值来决定：

``` python
a = 1  # 整型
a = 1.001  # 浮点数
a = "python"  # 字符串
print(a)  # 结果为“python”。print输出时只保留了最后一次的赋值
```

### import与from import

&emsp;&emsp;`python`用`import`或`from import`来导入相应的模块。

- 将整个模块导入，格式为`import somemodule`。
- 从某个模块中导入某个函数，格式为`from somemodule import somefunction`。
- 从某个模块中导入多个函数，格式为`from somemodule import firstfunc, secondfunc, thirdfunc`。
- 将某个模块中的全部函数导入，格式为`from somemodule import *`。

&emsp;&emsp;导入`sys`模块：

``` python
import sys

print('命令行参数为:')

for i in sys.argv:
    print(i)

print('\npython路径为', sys.path)
```

&emsp;&emsp;导入`sys`模块的`argv`和`path`函数：

``` python
from sys import argv, path  # 导入特定的成员
print('path:', path)  # 因为已经导入path成员，所以此处引用时不需要加“sys.path”
```