---
title: profile模块
categories: Python语法
date: 2018-12-27 18:07:25
---
&emsp;&emsp;`profile`用于统计程序中每一个函数的运行时间，并且提供了多样化的报表：<!--more-->

``` python
def foo():
    sum = 0

    for i in range(100000):
        sum += i

    return sum

if __name__ == "__main__":
    import profile
    profile.run("foo()")
```

执行结果：

``` bash
        5 function calls in 0.000 seconds

Ordered by: standard name

ncalls  tottime  percall  cumtime  percall filename:lineno(function)
    1    0.000    0.000    0.000    0.000 :0(exec)
    1    0.000    0.000    0.000    0.000 :0(setprofile)
    1    0.000    0.000    0.000    0.000 <string>:1(<module>)
    1    0.000    0.000    0.000    0.000 profile:0(foo())
    0    0.000             0.000          profile:0(profiler)
    1    0.000    0.000    0.000    0.000 test_python.py:1(foo)
```

&emsp;&emsp;`profile`统计结果如下：

列        | 说明                                    | 列                           | 说明
----------|-----------------------------------------|-----------------------------|-----
`ncalls`  | 函数的被调用次数                          | `tottime`                   | 函数总计运行时间，不含内部其它调用
`percall` | 函数运行一次的平均时间，即`tottime/ncalls` | `cumtime`                   | 函数总计运行时间，包含内部其它调用
`percall` | 函数运行一次的平均时间，即`cumtime/ncalls` | `filename:lineno(function)` | 函数所在的文件名、函数的行号和函数名