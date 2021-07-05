---
title: enum模块
categories: Python语法
date: 2018-12-28 09:30:36
---
### 枚举的定义

&emsp;&emsp;`enum`模块的用法如下：<!--more-->

``` python
from enum import Enum

class Color(Enum):
    red = 1
    orange = 2
    yellow = 3
    green = 4
    blue = 5
    indigo = 6
    purple = 7

red_member = Color.red
print(red_member.name)
print(red_member.value)
print("--------------")
for color in Color:
    print(color)
```

执行结果：

``` cpp
red
1
--------------
Color.red
Color.orange
Color.yellow
Color.green
Color.blue
Color.indigo
Color.purple
```

### 枚举常见问题

&emsp;&emsp;如果枚举中存在相同值的成员，在通过值获取枚举成员时，只能获取到第一个成员：

``` python
from enum import Enum

class Color(Enum):
    red = 1
    red_alias = 1

print(Color(1))  # 结果为“Color.red”
```

&emsp;&emsp;如果想要定义枚举时，不能定义相同值的成员，可以使用装饰器`@unique`：

``` python
from enum import Enum, unique

@unique
class Color(Enum):
    red = 1
    red_alias = 1
```

执行时就会提示错误：

``` bash
ValueError: duplicate values found in <enum 'Color'>: red_alias -> red
```