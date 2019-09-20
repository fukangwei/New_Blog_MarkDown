---
title: Shell函数
date: 2019-01-20 10:47:04
categories: Shell编程
---
&emsp;&emsp;`shell`可以用户定义函数，然后在`shell`脚本中可以随便调用。`shell`中函数的定义格式如下：

``` bash
[ function ] funname [()]
{
    action;
    [return int;]
}
```

- 可以使用`function fun()`定义，也可以直接使用`fun()`定义。
- 可以使用`return`返回整数(`0`至`255`)，如果不使用，将以最后一条命令运行结果作为返回值。

&emsp;&emsp;下面的例子定义了一个函数并进行调用：

``` bash
#!/bin/bash

demoFun() {
    echo "这是我的第一个 shell 函数!"
}

echo "-----函数开始执行-----"
demoFun
echo "-----函数执行完毕-----"
```

执行结果：

``` bash
-----函数开始执行-----
这是我的第一个 shell 函数!
-----函数执行完毕-----
```

下面定义一个带有`return`语句的函数：

``` bash
#!/bin/bash

funWithReturn(){
    echo "这个函数会对输入的两个数字进行相加运算..."
    echo "输入第一个数字: "
    read aNum
    echo "输入第二个数字: "
    read anotherNum
    echo "两个数字分别为 $aNum 和 $anotherNum !"
    return $(($aNum+$anotherNum))
}
​
funWithReturn
echo "输入的两个数字之和为 $? !"
```

执行结果：

``` bash
这个函数会对输入的两个数字进行相加运算...
输入第一个数字:
1
输入第二个数字:
2
两个数字分别为 1 和 2 !
输入的两个数字之和为 3 !
```

函数返回值在调用该函数后通过`$?`来获得。注意，所有函数在使用前必须定义，这意味着必须将函数放在脚本开始部分，直至`shell`解释器首次发现它时，才可以使用。

### 函数参数

&emsp;&emsp;在`Shell`中，调用函数时可以向其传递参数。在函数体内部，通过`$n`的形式来获取参数的值，例如`$1`表示第一个参数，`$2`表示第二个参数。

``` bash
#!/bin/bash

funWithParam(){
    echo "第一个参数为 $1 !"
    echo "第二个参数为 $2 !"
    echo "第十个参数为 $10 !"
    echo "第十个参数为 ${10} !"
    echo "第十一个参数为 ${11} !"
    echo "参数总数有 $# 个!"
    echo "作为一个字符串输出所有参数 $* !"
}
​
funWithParam 1 2 3 4 5 6 7 8 9 34 73
```

执行结果：

``` bash
第一个参数为 1 !
第二个参数为 2 !
第十个参数为 10 !
第十个参数为 34 !
第十一个参数为 73 !
参数总数有 11 个!
作为一个字符串输出所有参数 1 2 3 4 5 6 7 8 9 34 73 !
```

注意，`$10`不能获取第十个参数，获取第十个参数需要用`${10}`。当`n >= 10`时，需要使用`${n}`来获取参数。另外，还有几个特殊字符用来处理参数：

参数处理 | 说明
--------|--------
`$#`    | 传递到脚本的参数个数
`$*`    | 以一个单字符串显示所有向脚本传递的参数
`$$`    | 脚本运行的当前进程`ID`号
`$!`    | 后台运行的最后一个进程的`ID`号
`$@`    | 与`$*`相同，但是使用时加引号，并在引号中返回每个参数
`$-`    | 显示`Shell`使用的当前选项，与`set`命令功能相同
`$?`    | 显示最后命令的退出状态，`0`表示没有错误，其他任何值表明有错误

### 递归函数

&emsp;&emsp;`bash`也支持递归函数：

``` bash
#!/bin/bash

function name() {
    echo $1
    name hello
    sleep 1
}
​
name
```

运行此脚本后不断打印出`hello`，最后按`Ctrl + C`结束。

### 数组参数

&emsp;&emsp;`Shell`使用数组作为函数参数的方法如下：

``` bash
#!/bin/bash
​
function showArr(){
    arr=$1

    for i in ${arr[*]}; do
        echo $i
    done
}
​
regions=("GZ" "SH" "BJ")
showArr "${regions[*]}"
​
exit 0
```