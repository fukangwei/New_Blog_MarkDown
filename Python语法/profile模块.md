---
title: profile模块
categories: Python语法
date: 2018-12-27 18:07:25
---
&emsp;&emsp;`profile`是`python`的标准库，可以用于统计程序里每一个函数的运行时间，并且提供了多样化的报表。使用`profile`来分析一个程序很简单，例如有这样一个程序：<!--more-->

``` python
def foo():
    sum = 0

    for i in range(100000):
        sum += i

    return sum

if __name__ == "__main__":
    foo()
```

把`if`程序块改为如下：

``` python
if __name__ == "__main__":
    import profile
    profile.run("foo()")
```

执行结果：

``` bash
5 function calls in 0.016 seconds

Ordered by: standard name

ncalls  tottime  percall  cumtime  percall filename:lineno(function)
     1    0.000    0.000    0.016    0.016 :0(exec)
     1    0.000    0.000    0.000    0.000 :0(setprofile)
     1    0.000    0.000    0.016    0.016 <string>:1(<module>)
     1    0.016    0.016    0.016    0.016 const.py:1(foo)
     1    0.000    0.000    0.016    0.016 profile:0(foo())
     0    0.000             0.000          profile:0(profiler)
```

&emsp;&emsp;`profile`的统计结果分为`ncalls`、`tottime`、`percall`、`cumtime`、`percall`和`filename:lineno(function)`等若干列：

列                          | 说明
----------------------------|----
`ncalls`                    | 函数的被调用次数
`tottime`                   | 函数总计运行时间，除去函数中调用的函数运行时间
`percall`                   | 函数运行一次的平均时间，等于`tottime/ncalls`
`cumtime`                   | 函数总计运行时间，含调用的函数运行时间
`percall`                   | 函数运行一次的平均时间，等于`cumtime/ncalls`
`filename:lineno(function)` | 函数所在的文件名、函数的行号和函数名