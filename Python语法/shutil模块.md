---
title: shutil模块
categories: Python语法
date: 2018-12-27 19:21:58
---
&emsp;&emsp;`shutil`是高级的文件、文件夹、压缩包处理模块。<!--more-->

### copyfileobj

&emsp;&emsp;将文件内容拷贝到另一个文件中：

``` python
shutil.copyfileobj(fsrc, fdst[, length])
```

&emsp;&emsp;代码实例：

``` python
import shutil
shutil.copyfileobj(open('my_test.cpp', 'r'), open('hello.txt', 'w'))
```

### copyfile

&emsp;&emsp;拷贝文件：

``` python
shutil.copyfile(src, dst)
```

&emsp;&emsp;代码实例：

``` python
import shutil
shutil.copyfile('f1.log', 'f2.log')
```

### copy

&emsp;&emsp;拷贝文件和权限：

``` python
shutil.copy(src, dst)
```

&emsp;&emsp;代码实例：

``` python
import shutil
shutil.copy('f1.log', 'f2.log')
```

### copy2

&emsp;&emsp;拷贝文件和状态信息：

``` python
shutil.copy2(src, dst)
```

&emsp;&emsp;代码实例：

``` python
import shutil
shutil.copy2('f1.log', 'f2.log')
```

### rmtree

&emsp;&emsp;递归地删除文件夹：

``` python
shutil.rmtree(path)
```

&emsp;&emsp;代码实例：

``` python
import shutil
shutil.rmtree('./test')
```