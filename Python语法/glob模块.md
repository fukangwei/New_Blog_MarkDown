---
title: glob模块
categories: Python语法
date: 2019-02-09 14:53:05
---
&emsp;&emsp;`glob`模块用来查找目录或文件，并将查找的结果返回到一个列表中。<!--more-->
&emsp;&emsp;列举当前目录下的`csv`文件：

``` python
from glob import glob

def search():
    f = glob('*.csv')
    for i in f:
        print(i)

if __name__ == "__main__":
    search()
```