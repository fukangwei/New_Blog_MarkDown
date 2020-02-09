---
title: enum模块
categories: Python语法
abbrlink: 3e04fdf6
date: 2018-12-28 09:30:36
---
### 枚举的定义

&emsp;&emsp;首先枚举要导入`enum`模块，然后使用`class`关键字定义枚举类(继承`Enum`类)：<!--more-->

``` python
from enum import Enum
​
class Color(Enum):
    red = 1
    orange = 2
    yellow = 3
    green = 4
    blue = 5
    indigo = 6
    purple = 7
```

上面的代码定义了颜色的枚举类`Color`，其中有`7`个成员，分别是`Color.red`、`Color.orange`、`Color.yellow`等。每一个成员都有它们各自名称和值，`Color.red`成员的名称是`red`，值是`1`。每个成员的数据类型就是它所属的枚举。

### 枚举常见问题

&emsp;&emsp;定义枚举时，成员名称不允许重复：

``` python
from enum import Enum
​
class Color(Enum):
    red = 1
    red = 2
```

上述代码会提示错误`TypeError: Attempted to reuse key: 'red'`。
&emsp;&emsp;默认情况下，不同的成员值允许相同，第二个成员的名称被视作第一个成员的别名：

``` python
from enum import Enum
​
class Color(Enum):
    red = 1
    red_alias = 1
```

&emsp;&emsp;如果枚举中存在相同值的成员，在通过值获取枚举成员时，只能获取到第一个成员：

``` python
from enum import Enum
​
class Color(Enum):
    red = 1
    red_alias = 1
​
print(Color(1))  # 结果为“Color.red”
```

&emsp;&emsp;如果要限制定义枚举时，不能定义相同值的成员。可以使用装饰器`@unique`(要导入`unique`模块)：

``` python
from enum import Enum, unique
​
@unique
class Color(Enum):
    red = 1
    red_alias = 1
```

执行时就会提示错误：

``` bash
ValueError: duplicate values found in <enum 'Color'>: red_alias -> red
```

### 枚举取值

&emsp;&emsp;通过成员的名称来获取成员：

``` python
Color['red']
```

&emsp;&emsp;通过成员值来获取成员：

``` python
Color(2)
```

&emsp;&emsp;通过成员来获取它的名称和值：

``` python
red_member = Color.red
red_member.name
red_member.value
```

### 迭代器

&emsp;&emsp;枚举支持迭代器，可以遍历枚举成员：

``` python
for color in Color:
    print(color)
```

输出结果是枚举的所有成员，例如`Color.red`。
&emsp;&emsp;如果枚举有值重复的成员，循环遍历枚举时只获取值重复成员的第一个成员。如果想把值重复的成员也遍历出来，要用枚举的一个特殊属性`_members_`：

``` python
from enum import Enum
​
class Color(Enum):
    red = 1
    orange = 2
    yellow = 3
    red_alias = 1
​
for color in Color.__members__.items():
    print(color)
```

执行结果：

``` bash
('red', <Color.red: 1>)
('orange', <Color.orange: 2>)
('yellow', <Color.yellow: 3>)
('red_alias', <Color.red: 1>)
```