---
title: getopts简介
categories: Shell编程
abbrlink: 5d948048
date: 2019-01-20 06:40:00
---
&emsp;&emsp;由于`shell`命令行的灵活性，自己编写代码判断时，复杂度会比较高。使用内部命令`getopts`可以很方便地处理命令行参数。

``` bash
getopts options variable
```

`getopts`的设计目标是在循环中运行，每次执行循环，`getopts`就检查下一个命令行参数，并判断它是否合法，即检查参数是否以`-`开头，后面跟一个包含在`options`中的字母。如果是，就把匹配的选项字母存在指定的变量`variable`中，并返回退出状态`0`；如果`-`后面的字母没有包含在`options`中，就在`variable`中存入一个`?`，并返回退出状态`0`；如果命令行中已经没有参数，或者下一个参数不以`-`开头，就返回不为`0`的退出状态。

``` bash
#!/bin/bash
​
while getopts h:ms option
do
    case "$option" in
        h)
            echo "option:h, value $OPTARG"
            echo "next arg index:$OPTIND"
            ;;
        m)
            echo "option:m"
            echo "next arg index:$OPTIND"
            ;;
        s)
            echo "option:s"
            echo "next arg index:$OPTIND"
            ;;
        \?)
            echo "Usage: args [-h n] [-m] [-s]"
            echo "-h means hours"
            echo "-m means minutes"
            echo "-s means seconds"
            exit 1
            ;;
    esac
done
​
echo "*** do something now ***"
```

执行结果：

``` bash
$ ./args -h 100 -ms
option:h, value 100
next arg index:3
option:m
next arg index:3
option:s
next arg index:4
*** do something now ***
​
$ ./args -t
./args: illegal option -- t
Usage: args [-h n] [-m] [-s]
-h means hours
-m means minutes
-s means seconds
```

&emsp;&emsp;注意如下几点：
&emsp;&emsp;1. `getopts`允许把选项堆叠在一起，如`-ms`。
&emsp;&emsp;2. 如果要带参数，须在对应选项后加`:`，例如上例中的`h`。此时选项和参数之间至少有一个空白字符分隔，这样的选项不能堆叠。
&emsp;&emsp;3. 如果在需要参数的选项之后没有找到参数，它就在给定的变量中存入`?`，并向标准错误中写入错误消息，否则将实际参数写入特殊变量`OPTARG`。
&emsp;&emsp;4. 另外一个特殊变量`OPTIND`反映下一个要处理的参数索引，初值是`1`，每次执行`getopts`时都会更新。