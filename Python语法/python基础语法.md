---
title: python基础语法
date: 2019-01-10 08:54:28
categories: Python语法
---
### 编码

&emsp;&emsp;默认情况下，`Python 3`源码文件以`UTF-8`编码保存，所有的字符串都是`unicode`字符串。当然你也可以为源码文件指定不同的编码：

``` python
# -*- coding: cp-1252 -*-
```

### 标识符

- 第一个字符必须是英文字母表中的`字母`或`_`。
- 标识符的其他的部分可以是`字母`、`数字`或`_`。
- 标识符对大小写敏感。
- 在`Python 3`中，非`ASCII`标识符也是允许的。

### python保留字

&emsp;&emsp;保留字即关键字，我们不能把它们用作任何标识符名称。`Python`的标准库提供了`keyword`模块，可以输出当前版本的所有关键字：

``` python
>>> import keyword
>>> keyword.kwlist
['False', 'None', 'True', 'and', 'as', 'assert', 'break', 'class', 'continue', 'def', 'del',
'elif', 'else', 'except', 'finally', 'for', 'from', 'global', 'if', 'import', 'in', 'is', 'lambda',
'nonlocal', 'not', 'or', 'pass', 'raise', 'return', 'try', 'while', 'with', 'yield']
```

### 注释

&emsp;&emsp;`Python`中单行注释以`#`开头：

``` python
# 第一个注释
print("Hello, Python!")  # 第二个注释
```

多行注释可以用多个`#`号：

``` python
# 第一个注释
# 第二个注释
print("Hello, Python!")
```

关于注释，也可以使用`''' '''`(或`""" """`)的格式在三引号之间书写较长的注释。该方法还可以用于在函数首部，对函数进行一个说明：

``` python
def example(anything):
    ''' 形参为任意类型的对象，这个示例函数会将其原样返回 '''
    return anything
```

### 行与缩进

&emsp;&emsp;`python`的特色就是使用缩进来表示代码块，不需要使用大括号`{}`。缩进的空格数是可变的，但是属于同一个代码块的语句必须使用相同的缩进空格数：

``` python
if True:
    print("True")
else:
    print("False")
```

以下代码最后一行语句缩进数的空格数不一致，会导致运行错误：

``` python
if True:
    print("Answer")
    print("True")
else:
    print("Answer")
  print("False")  # 缩进不一致，会导致运行错误
```

### 多行语句

&emsp;&emsp;`Python`通常是一行写完一条语句，但如果语句很长，我们可以使用反斜杠`\`来实现多行语句：

``` python
total = item_one + \
        item_two + \
        item_three
```

在`[]`、`{}`或`()`中的多行语句，不需要使用反斜杠`\`：

``` python
total = ['item_one', 'item_two', 'item_three',
         'item_four', 'item_five']
```

### 空行

&emsp;&emsp;函数之间或类的方法之间用空行分隔，表示一段新的代码的开始。类和函数入口之间也用一行空行分隔，以突出函数入口的开始。
&emsp;&emsp;空行与代码缩进不同，空行并不是`Python`语法的一部分。书写时不插入空行，`Python`解释器运行也不会出错。但是空行的作用在于分隔两段不同功能或含义的代码，便于日后代码的维护或重构。记住，空行也是程序代码的一部分。

### 等待用户输入

&emsp;&emsp;执行下面的程序，显示`按下enter键后退出`后会等待用户输入：

``` python
input("按下enter键后退出")
```

一旦用户按下回车键，程序将退出。

### 同一行显示多条语句

&emsp;&emsp;`Python`可以在同一行中使用多条语句，语句之间使用分号`;`分割：

``` python
import sys; x = 'runoob'; sys.stdout.write(x + '\n')
```

### 多个语句构成代码组

&emsp;&emsp;缩进相同的一组语句构成一个代码块，我们称之为`代码组`。像`if`、`while`、`def`和`class`这样的复合语句，首行以关键字开始，以冒号`:`结束，该行之后的一行或多行代码构成代码组。我们将首行以及后面的代码组称为一个`子句`(`clause`)。

``` python
if expression:
    suite
elif expression:
    suite
else:
    suite
```

### Print输出

&emsp;&emsp;print默认输出是换行的，如果要实现不换行，需要在`print`中加上`end=" "`：

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

&emsp;&emsp;`python`的变量不需要声明变量类型，变量的类型由赋予它的值来决定：

``` python
a = 1  # 整型
a = 1.001  # 浮点数
a = "python"  # 字符串
print(a)  # 结果为“python”。print输出时只保留了最后一次的赋值
```

### import与from-import

&emsp;&emsp;`python`用`import`或`from-import`来导入相应的模块。

- 将整个模块(`somemodule`)导入，格式为`import somemodule`。
- 从某个模块中导入某个函数，格式为`from somemodule import somefunction`。
- 从某个模块中导入多个函数，格式为`from somemodule import firstfunc, secondfunc, thirdfunc`。
- 将某个模块中的全部函数导入，格式为`from somemodule import *`。

&emsp;&emsp;导入`sys`模块：

``` python
import sys
​
print('命令行参数为:')

for i in sys.argv:
    print(i)

print('\npython路径为', sys.path)
```

&emsp;&emsp;导入`sys`模块的`argv`和`path`函数：

``` python
from sys import argv, path  # 导入特定的成员
​
print('path:', path)  # 因为已经导入path成员，所以此处引用时不需要加“sys.path”
```