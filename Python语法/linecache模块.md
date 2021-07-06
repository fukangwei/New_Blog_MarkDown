---
title: linecache模块
date: 2021-02-22 07:22:51
categories: Python语法
---
&emsp;&emsp;如果我们想读取某个文件的指定行，就可以使用`linecache`模块。<!--more-->
&emsp;&emsp;使用该模块读取的文件，其编码格式也必须为`UTF-8`。
&emsp;&emsp;`linecache`常用的函数如下：

- `linecache.getline(filename, lineno, module_globals=None)`：读取指定模块中指定文件的指定行。

1. `filename`：指定文件名。
2. `lineno`：指定行号。
3. `module_globals`：指定要读取的模块名。

- `linecache.clearcache()`：如果程序不再需要之前使用`getline`函数读取的数据，则可以使用该函数清空缓存。

``` python
import linecache
import string

# 读取string模块中第3行的数据
print(linecache.getline(string.__file__, 3))
# 读取普通文件的第2行
print(linecache.getline('my_file.txt', 2))
```