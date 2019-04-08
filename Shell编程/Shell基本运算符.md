---
title: Shell基本运算符
date: 2019-01-20 12:22:58
tags:
---
&emsp;&emsp;原生`bash`不支持简单的数学运算，但是可以通过其他命令来实现，例如`expr`。`expr`是一款表达式计算工具，使用它能完成表达式的求值操作。例如，两个数相加(注意使用的是反引号，而不是单引号)：

``` bash
#!/bin/bash

val=`expr 2 + 2`
echo "两数之和为: $val"  # 输出“两数之和为: 4”
```

&emsp;&emsp;注意，表达式和运算符之间要有空格，例如`2+2`是不对的，必须写成`2 + 2`。

### 算术运算符

&emsp;&emsp;下表列出了常用的算术运算符，假定变量`a`为`10`，变量`b`为`20`：

运算符 |说明    | 举例
------|--------|---------
`+`   | 加法   | <code>\`expr &#36;a + &#36;b\`</code>结果为`30`
`-`   | 减法   | <code>\`expr &#36;a - &#36;b\`</code>结果为`-10`
`*`   | 乘法   | <code>\`expr &#36;a \\* &#36;b\`</code>结果为`200`
`/`   | 除法   | <code>\`expr &#36;b / &#36;a\`</code>结果为`2`
`%`   | 取余   | <code>\`expr &#36;b % &#36;a\`</code>结果为`0`
`=`   | 赋值   | `a=$b`将把变量`b`的值赋给`a`
`==`  | 相等   | `[$a == $b]`返回`false`
`!=`  | 不相等 | `[$a != $b]`返回`true`

注意，条件表达式要放在方括号之间，并且要有空格。例如`[$a==$b]`是错误的，必须写成`[ $a == $b ]`。

``` bash
#!/bin/bash

a=10
b=20

val=`expr $a + $b`
echo "a + b : $val"

val=`expr $a - $b`
echo "a - b : $val"

val=`expr $a \* $b`
echo "a * b : $val"

val=`expr $b / $a`
echo "b / a : $val"

val=`expr $b % $a`
echo "b % a : $val"

if [ $a == $b ]
then
   echo "a 等于 b"
fi
if [ $a != $b ]
then
   echo "a 不等于 b"
fi
```

注意，乘号`*`前边必须加反斜杠`\`才能实现乘法运算。在`MAC`中，`shell`的`expr`语法是`$((表达式))`，此处表达式中的`*`不需要转义符号`\`。

### 关系运算符

&emsp;&emsp;关系运算符只支持数字，不支持字符串，除非字符串的值是数字。下表列出了常用的关系运算符，假定变量`a`为`10`，变量`b`为`20`：

运算符 | 说明                        | 举例
------|-----------------------------|-----
`-eq` | 检测两个数是否相等            | `[$a -eq $b]`返回`false`
`-ne` | 检测两个数是否不相等          | `[$a -ne $b]`返回`true`
`-gt` | 检测左边的数是否大于右边的     | `[$a -gt $b]`返回`false`
`-lt` | 检测左边的数是否小于右边的     | `[$a -lt $b]`返回`true`
`-ge` | 检测左边的数是否大于等于右边的 | `[$a -ge $b]`返回`false`
`-le` | 检测左边的数是否小于等于右边的 | `[$a -le $b]`返回`true`

``` bash
#!/bin/bash

a=10
b=20

if [ $a -eq $b ]
then
   echo "$a -eq $b : a 等于 b"
else
   echo "$a -eq $b: a 不等于 b"
fi
if [ $a -ne $b ]
then
   echo "$a -ne $b: a 不等于 b"
else
   echo "$a -ne $b : a 等于 b"
fi
if [ $a -gt $b ]
then
   echo "$a -gt $b: a 大于 b"
else
   echo "$a -gt $b: a 不大于 b"
fi
if [ $a -lt $b ]
then
   echo "$a -lt $b: a 小于 b"
else
   echo "$a -lt $b: a 不小于 b"
fi
if [ $a -ge $b ]
then
   echo "$a -ge $b: a 大于或等于 b"
else
   echo "$a -ge $b: a 小于 b"
fi
if [ $a -le $b ]
then
   echo "$a -le $b: a 小于或等于 b"
else
   echo "$a -le $b: a 大于 b"
fi
```

### 布尔运算符

&emsp;&emsp;下表列出了常用的布尔运算符，假定变量`a`为`10`，变量`b`为`20`：

运算符 | 说明 | 举例
------|-------|-----------
`!`   | 非运算 | `[! false]`返回`true`
`-o`  | 或运算 | `[$a -lt 20 -o $b -gt 100]`返回`true`
`-a`  | 与运算 | `[$a -lt 20 -a $b -gt 100]`返回`false`

``` bash
#!/bin/bash

a=10
b=20

if [ $a != $b ]
then
   echo "$a != $b : a 不等于 b"
else
   echo "$a != $b: a 等于 b"
fi
if [ $a -lt 100 -a $b -gt 15 ]
then
   echo "$a -lt 100 -a $b -gt 15 : 返回 true"
else
   echo "$a -lt 100 -a $b -gt 15 : 返回 false"
fi
if [ $a -lt 100 -o $b -gt 100 ]
then
   echo "$a -lt 100 -o $b -gt 100 : 返回 true"
else
   echo "$a -lt 100 -o $b -gt 100 : 返回 false"
fi
if [ $a -lt 5 -o $b -gt 100 ]
then
   echo "$a -lt 100 -o $b -gt 100 : 返回 true"
else
   echo "$a -lt 100 -o $b -gt 100 : 返回 false"
fi
```

### 逻辑运算符

&emsp;&emsp;以下介绍`Shell`的逻辑运算符，假定变量`a`为`10`，变量`b`为`20`。

运算符                    | 说明        | 举例
--------------------------|------------|-----
`&&`                      | 逻辑的`AND` | `[[ $a -lt 100 && $b -gt 100 ]]`返回`false`
<code>&#124;&#124;</code> | 逻辑的`OR`  | <code>\[\[ &#36;a -lt 100 &#124;&#124; &#36;b -gt 100 \]\]</code>返回`true`

``` bash
#!/bin/bash

a=10
b=20

if [[ $a -lt 100 && $b -gt 100 ]]
then
   echo "返回 true"
else
   echo "返回 false"
fi

if [[ $a -lt 100 || $b -gt 100 ]]
then
   echo "返回 true"
else
   echo "返回 false"
fi
```

### 字符串运算符

&emsp;&emsp;下表列出了常用的字符串运算符，假定变量`a`为`abc`，变量`b`为`efg`：

运算符 | 说明                    | 举例
------|-------------------------|-----
`=`   | 检测两个字符串是否相等    | `[$a = $b]`返回`false`
`!=`  | 检测两个字符串是否不相等  | `[$a != $b]`返回`true`
`-z`  | 检测字符串长度是否为`0`   | `[-z $a]`返回`false`
`-n`  | 检测字符串长度是否不为`0` | `[-n $a]`返回`true`
`str` | 检测字符串是否不为空      | `[$a]`返回`true`

``` bash
#!/bin/bash

a="abc"
b="efg"

if [ $a = $b ]
then
   echo "$a = $b : a 等于 b"
else
   echo "$a = $b: a 不等于 b"
fi
if [ $a != $b ]
then
   echo "$a != $b : a 不等于 b"
else
   echo "$a != $b: a 等于 b"
fi
if [ -z $a ]
then
   echo "-z $a : 字符串长度为 0"
else
   echo "-z $a : 字符串长度不为 0"
fi
if [ -n $a ]
then
   echo "-n $a : 字符串长度不为 0"
else
   echo "-n $a : 字符串长度为 0"
fi
if [ $a ]
then
   echo "$a : 字符串不为空"
else
   echo "$a : 字符串为空"
fi
```

### 文件测试运算符

&emsp;&emsp;文件测试运算符用于检测`Unix`文件的各种属性：

操作符     | 说明                                          | 举例
----------|-----------------------------------------------|-----
`-b file` | 检测文件是否是块设备文件                         | `[ -b $file ]`返回`false`
`-c file` | 检测文件是否是字符设备文件                       | `[ -c $file ]`返回`false`
`-d file` | 检测文件是否是目录                              | `[ -d $file ]`返回`false`
`-f file` | 检测文件是否是普通文件(既不是目录，也不是设备文件) | `[ -f $file ]`返回`true`
`-g file` | 检测文件是否设置了`SGID`位                       | `[ -g $file ]`返回`false`
`-k file` | 检测文件是否设置了粘着位(`Sticky Bit`)           | `[ -k $file ]`返回`false`
`-p file` | 检测文件是否是有名管道                           | `[ -p $file ]`返回`false`
`-u file` | 检测文件是否设置了`SUID`位                       | `[ -u $file ]`返回`false`
`-r file` | 检测文件是否可读                                 | `[ -r $file ]`返回`true`
`-w file` | 检测文件是否可写                                 | `[ -w $file ]`返回`true`
`-x file` | 检测文件是否可执行                               | `[ -x $file ]`返回`true`
`-s file` | 检测文件是否不为空(文件大小是否大于`0`)            | `[ -s $file ]`返回`true`
`-e file` | 检测文件(包括目录)是否存在                        | `[ -e $file ]`返回`true`

变量`file`表示文件`/var/www/runoob/test.sh`，它的大小为`100`字节，具有`rwx`权限。下面的代码将检测该文件的各种属性：

``` bash
#!/bin/bash

file="/var/www/runoob/test.sh"
if [ -r $file ]
then
   echo "文件可读"
else
   echo "文件不可读"
fi
if [ -w $file ]
then
   echo "文件可写"
else
   echo "文件不可写"
fi
if [ -x $file ]
then
   echo "文件可执行"
else
   echo "文件不可执行"
fi
if [ -f $file ]
then
   echo "文件为普通文件"
else
   echo "文件为特殊文件"
fi
if [ -d $file ]
then
   echo "文件是个目录"
else
   echo "文件不是个目录"
fi
if [ -s $file ]
then
   echo "文件不为空"
else
   echo "文件为空"
fi
if [ -e $file ]
then
   echo "文件存在"
else
   echo "文件不存在"
fi
```

### let命令

&emsp;&emsp;`let`命令是`bash`中用于计算的工具，提供常用运算符以及幂`**`运算符。在计算中不需要加上`$`来表示变量，如果表达式的值是非`0`，那么返回的状态值是`0`；否则返回的状态值是`1`。

``` bash
let arg [arg ...]  # arg代表运算式
```

自加操作`let no++`，自减操作`let no--`。简写形式`let no+=10`和`let no-=20`分别等同于`let no=no+10`和`let no=no-20`。

### 按位操作运算符

&emsp;&emsp;位运算符通常用于整数间运算，位运算符是针对整数在内存中二进制数据流中的位进行操作。例如表达式`2>>1`表示将整数的二进制数`2`在内存中的二进制数据流向左移动一位。

运算符               | 名称     | 举例
--------------------|----------|------
`<<`                | 左移     | `value=4>>2`
`>>`                | 右移     | `value=8<<2`
`&`                 | 按位与   | `value=8&4`
<code>&#124;</code> | 按位或   | <code>value=8&#124;4</code>
`~`                 | 按位非   | `value=~8`
`^`                 | 按位异或 | `value=10^3`