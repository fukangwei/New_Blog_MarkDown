---
title: linecache模块
date: 2021-02-22 07:22:51
categories: Python语法
---
&emsp;&emsp;如果我们想读取某个文件中指定行包含的数据，就可以使用`linecache`模块。<!--more-->
&emsp;&emsp;值得一提的是，`linecache`模块常用来读取`Python`源文件中的代码，它使用的是`UTF-8`编码格式来读取文件内容。这意味着，使用该模块读取的文件，其编码格式也必须为`UTF-8`，否则要么读取出来的数据是乱码，要么直接读取失败(`Python`解释器会报`SyntaxError`异常)。
&emsp;&emsp;要使用`linecache`模块，就必须知道其包含了哪些函数。`linecache`模块中常用的函数及其功能如下所示：

- `linecache.getline(filename, lineno, module_globals=None)`：读取指定模块中指定文件的指定行。

1. `filename`：指定文件名。
2. `lineno`：指定行号。
3. `module_globals`：指定要读取的具体模块名。

- `linecache.clearcache()`：如果程序不再需要之前使用`getline`函数读取的数据，则可以使用该函数清空缓存。
- `linecache.checkcache(filename=None)`：检查缓存的有效性，即如果使用`getline`函数读取的数据，其实在本地已经被修改，而我们需要的是新的数据，此时就可以使用该函数检查缓存的是否为新的数据。注意，如果省略文件名，该函数将检查所有缓存数据的有效性。

``` python
import linecache
import string

# 读取string模块中第3行的数据
print(linecache.getline(string.__file__, 3))
# 读取普通文件的第2行
print(linecache.getline('my_file.txt', 2))
```

在执行该程序之前，需保证`my_file.txt`文件是以`UTF-8`编码格式保存的。