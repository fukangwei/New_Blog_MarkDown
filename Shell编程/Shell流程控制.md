---
title: Shell流程控制
date: 2019-01-21 09:41:20
categories: Shell编程
---
### if else

&emsp;&emsp;`if`语法格式如下：

``` bash
if condition
then
    command1
    command2
    ...
    commandN
fi
```

写成一行(适用于终端命令提示符)：

``` bash
if [ $(ps -ef | grep -c "ssh") -gt 1 ]; then echo "true"; fi
```

&emsp;&emsp;`if else`语法格式如下：

``` bash
if condition
then
    command1
    command2
    ...
    commandN
else
    command
fi
```

&emsp;&emsp;`if else-if else`语法格式如下：

``` bash
if condition1
then
    command1
elif condition2
then
    command2
else
    commandN
fi
```

以下实例判断两个变量是否相等：

``` bash
#!/bin/bash
​
a=10
b=20
if [ $a == $b ]
then
    echo "a 等于 b"
elif [ $a -gt $b ]
then
    echo "a 大于 b"
elif [ $a -lt $b ]
then
    echo "a 小于 b"
else
   echo "没有符合的条件"
fi
```

执行结果为`a 小于 b`。
&emsp;&emsp;`if else`语句经常与`test`命令结合使用：

``` bash
#!/bin/bash
​
num1=$[2*3]
num2=$[1+5]
​
if test $[num1] -eq $[num2]
then
    echo '两个数字相等!'
else
    echo '两个数字不相等!'
fi
```

执行结果为`两个数字相等!`。

### for

&emsp;&emsp;`for`循环格式如下：

``` bash
for var in item1 item2 ... itemN
do
    command1
    command2
    ...
    commandN
done
```

写成一行为：

``` bash
for var in item1 item2 ... itemN; do command1; command2… done;
```

当变量值在列表里，`for`循环即执行一次所有命令，使用变量名获取列表中的当前取值。命令可为任何有效的`shell`命令和语句。`in`列表可以包含数值、字符串和文件名。`in`列表是可选的，如果不用它，`for`循环使用命令行的位置参数。例如，顺序输出当前列表中的数字：

``` bash
for loop in 1 2 3 4 5
do
    echo "The value is: $loop"
done
```

执行结果：

``` bash
The value is: 1
The value is: 2
The value is: 3
The value is: 4
The value is: 5
```

顺序输出字符串中的字符：

``` bash
for str in 'This is a string'
do
    echo $str
done
```

执行结果为`This is a string`。

### while

&emsp;&emsp;`while`循环格式如下：

``` bash
while condition
do
    command
done
```

示例如下：

``` bash
#!/bin/sh
int=1
while(( $int<=5 ))
do
    echo $int
    let "int++"
done
```

执行结果：

``` bash
1
2
3
4
5
```

&emsp;&emsp;`while`循环可用于读取键盘信息：

``` bash
#!/bin/sh
​
echo '按下 <CTRL-D> 退出'
echo -n '输入你最喜欢的电影名: '
while read FILM
do
    echo "是的！$FILM 是一部好电影"
done
```

执行结果：

``` bash
按下 <CTRL-D> 退出
输入你最喜欢的电影名: 泰坦尼克
是的！泰坦尼克 是一部好电影
```

### 无限循环

&emsp;&emsp;无限循环语法格式：

``` bash
while :
do
    command
done
# 或者
while true
do
    command
done
# 或者
for (( ; ; ))
```

### until

&emsp;&emsp;`until`循环执行一系列命令直至条件为真时停止，与`while`循环在处理方式上刚好相反：

``` bash
until condition
do
    command
done
```

条件可为任意测试条件，测试发生在循环末尾，因此循环至少执行一次。

``` bash
#!/bin/bash
​
i=0
​
until [[ "$i" -gt 5 ]]
do
    let "square=i*i"
    echo "$i * $i = $square"
    let "i++"
done
```

### case

&emsp;&emsp;`case`语句为多选择语句，可以用`case`语句匹配一个值与一个模式，如果匹配成功，执行相匹配的命令。

``` bash
case 值 in
模式1)
    command1
    command2
    # ...
    commandN
    ;;
模式2)
    command1
    command2
    # ...
    commandN
    ;;
esac
```

值的后面必须为单词`in`，每一种模式必须以右括号结束，值可以为变量或常数。匹配发现其值符合某一模式后，其间所有命令开始执行直至`;;`。如果无一匹配模式，使用星号`*`捕获该值，再执行后面的命令。

``` bash
#!/bin/sh
​
echo '输入 1 到 4 之间的数字:'
echo '你输入的数字为:'
read aNum
case $aNum in
    1) echo '你选择了 1'
    ;;
    2) echo '你选择了 2'
    ;;
    3) echo '你选择了 3'
    ;;
    4) echo '你选择了 4'
    ;;
    *) echo '你没有输入 1 到 4 之间的数字'
    ;;
esac
```

使用`case`测试字符串范围：

``` bash
#!/bin/bash  
​
echo "Hit a key, then hit return."  
read Keypress  
  
case "$Keypress" in
    [[:lower:]] ) echo "Lowercase letter";;  # 小写字母
    [[:upper:]] ) echo "Uppercase letter";;  # 大写字母
    [0-9]       ) echo "Digit";;  # 单个数字
    *           ) echo "Punctuation, whitespace, or other";;  # 其他字符  
esac
​
exit 0
```

### 跳出循环

&emsp;&emsp;在循环过程中，有时候需要在未达到循环结束条件时强制跳出循环。`Shell`使用两个命令来实现该功能，即`break`和`continue`。
&emsp;&emsp;`break`命令允许跳出所有循环(终止执行后面的所有循环)。下面的例子中，脚本进入死循环直至用户输入数字大于`5`：

``` bash
#!/bin/bash
​
while :
do
    echo -n "输入 1 到 5 之间的数字:"
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的! 游戏结束"
            break
        ;;
    esac
done
```

&emsp;&emsp;`continue`命令与`break`命令类似，只有一点差别，它不会跳出所有循环，仅仅跳出当前循环。对上面的例子进行修改：

``` bash
#!/bin/bash
while :
do
    echo -n "输入 1 到 5 之间的数字: "
    read aNum
    case $aNum in
        1|2|3|4|5) echo "你输入的数字为 $aNum!"
        ;;
        *) echo "你输入的数字不是 1 到 5 之间的!"
            continue
            echo "游戏结束"
        ;;
    esac
done
```

运行代码发现，当输入大于`5`的数字时，该例中的循环不会结束，语句`echo "游戏结束"`永远不会被执行。

---

&emsp;&emsp;列表`for`循环如下：

``` bash
#!/bin/bash  
​
for varible1 in {1..5}  # for varible1 in 1 2 3 4 5  
do
    echo "Hello, Welcome $varible1 times"
done
```

&emsp;&emsp;`Shell`支持列表`for`循环使用略写的计数方式，`1`至`5`的范围用`{1..5}`表示(大括号不能去掉，否则会当作一个字符串处理)。`Shell`中还支持按规定的步数进行跳跃的方式实现列表`for`循环，例如计算`1`至`100`内所有的奇数之和：

``` bash
#!/bin/bash
sum=0  
​
for i in {1..100..2}  
do
    let "sum+=i"  
done

echo "sum=$sum"  
```

通过`i`的按步数`2`不断递增，计算`sum`值为`2500`。同样可以使用`seq`命令实现按`2`递增来计算`1`至`100`内的所有奇数之和，即`for i in $(seq 1 2 100)`。
&emsp;&emsp;`for`循环对字符串进行操作，例如通过`for`循环显示当前目录下所有的文件：

``` bash
#!/bin/bash
​
for file in $( ls )
do
    echo "file: $file"
done
```

&emsp;&emsp;`for`通过命令行来传递脚本中`for`循环列表参数：

``` bash
#!/bin/bash
​
echo "number of arguments is $#"
echo "What you input is: "
​
for argument in "$@"
do
    echo "$argument"
done
```

`$#`表示参数的个数，`$@`表示参数列表，而`$*`则把所有的参数当作一个字符串显示。
&emsp;&emsp;不带列表`for`循环由用户制定参数和参数的个数，与上述的`for`循环列表参数功能相同。

``` bash
#!/bin/bash
​
echo "number of arguments is $#"
echo "What you input is: "
​
for argument
do
    echo "$argument"
done
```

比上述代码少了`$@`参数列表和`$*`参数字符串。
&emsp;&emsp;类`C`风格的`for`循环也被称为`计次循环`：

``` bash
#!/bin/bash  

for((integer=1; integer<=5; integer++))  
do  
    echo "$integer"
done
```

`for`中第一个表达式`integer=1`是循环变量赋初值的语句，第二个表达式`integer<=5`决定是否进行循环的表达式，退出状态为非`0`时将退出`for`循环执行`done`后的命令(与`C`中的`for`循环条件是刚好相反的)，第三个表达式`integer++`用于改变循环变量的语句。
&emsp;&emsp;`Shell`中不运行使用非整数类型的数作为循环变量的代码，循环条件被忽略则默认的退出状态是`0`，`for((;;))`为死循环。类`C`的`for`循环计算`1`至`100`内所有的奇数之和：

``` bash
#!/bin/bash
​
sum=0
​
for(( i=1; i<=100; i=i+2 ))
do
    let "sum += i"
done
​
echo "sum=$sum"
```

### 循环嵌套

&emsp;&emsp;一个循环体内又包含另一个完整的循环结构，在外部循环的每次执行过程中都会触发内部循环，`for`、`while`、`until`可以相互嵌套。嵌套循环实现九九乘法表如下：

``` bash
#!/bin/bash
​
for (( i=1; i<=9; i++ ))
do
    for (( j=1; j <= i; j++ ))
    do
        let "temp = i * j"
        echo -n "$i*$j=$temp  "
    done

    echo ""  # output newline
done
```