---
title: Shell数组
categories: Shell编程
date: 2019-01-21 15:46:11
---
&emsp;&emsp;`bash`支持一维数组，不支持多维数组，并且没有限定数组的大小。类似于`C`语言，数组元素的下标由`0`开始编号。获取数组中的元素要利用下标，下标可以是整数或算术表达式，其值应大于或等于`0`。<!--more-->

### 定义数组

&emsp;&emsp;在`Shell`中，用括号来表示数组，数组元素用`空格`符号分割开。定义数组的一般形式为：

``` bash
数组名=(值1 值2 ... 值n)
```

示例如下：

``` bash
array_name=(value0 value1 value2 value3)
# 或者如下
array_name=(
    value0
    value1
    value2
    value3
)
```

还可以单独定义数组的各个分量：

``` bash
array_name[0]=value0
array_name[1]=value1
array_name[n]=valuen
```

还可以通过键值定义：

``` bash
array=([0]=value0 [1]=value1)
```

### 读取数组

&emsp;&emsp;读取数组元素值的格式如下：

``` bash
${数组名[下标]}
```

访问第一个数组元素有`2`个方法：

``` bash
echo "${array}"
# 或者
echo "${array[0]}"
```

示例如下：

``` bash
valuen=${array_name[n]}
```

使用`@`符号可以获取数组中的所有元素：

``` bash
echo ${array_name[@]}
```

示例如下：

``` bash
#! /bin/bash

for i in {1..10}
do
    array[$i]=$i
done

echo "${array[@]}"  # 输出元素的值
```

引用所有数组元素：

``` bash
#! /bin/bash

array=(Mon Tue WedThu Fri Sat Sun)

for e in "${array[@]}"  # 通过循环输出所有的数组元素
do
    echo "$e"
done
```

以切片部分获取部分数组元素：

``` bash
${array[@|*]:start:length}  # start表示起始元素的下标，lengh表示要截取的数组元素的个数
```

数组元素的替换：

``` bash
${array[@|*]/pattern/replacement}  # pattern表示要搜索的字符串，replacement表示用来替换的字符串
```

示例如下：

``` bash
#! /bin/bash

a=(1 2 3 4 5)  # 定义数组
echo "theresult is ${a[@]/3/100}"  # 输出替换结果
echo "the oldarray is ${a[@]}"  # 输出原始数组

a=(${a[@]/3/100})  # 将替换结果赋给一个数组变量
echo "the newarray is ${a[@]}"  # 输出新的数组变量的值
```

### 获取数组长度

&emsp;&emsp;获取数组长度的方法与获取字符串长度的方法相同：

``` bash
length=${#array_name[@]}  # 取得数组元素的个数
# 或者
length=${#array_name[*]}
lengthn=${#array_name[n]} # 取得数组单个元素的长度
```

### 删除数组

&emsp;&emsp;代码如下：

``` bash
unset array[n]  # 删除指定数组元素
unset array  # 删除整个数组
```

### 复制数组

&emsp;&emsp;复制数组的格式如下：

``` bash
newarray=("${array[@]}")
```

示例如下：

``` bash
linux=("Debian" "RedHat" "Ubuntu" "Suse" "Fedora" "UTS" "CentOS")
linux2=("${linux[@]}")  # 复制数组
echo "${linux2[@]}"
```

### 连接数组

&emsp;&emsp;连接数组的格式如下：

``` bash
("$array1[@]" "${array2[@]}")
```

示例如下：

``` bash
linux=("Debian" "RedHat" "Ubuntu" "Suse" "Fedora" "UTS" "CentOS")
shell=("bash" "csh" "ksh" "rsh" "sh" "rc" "tcsh")

linuxshell=("${linux[@]}" "${shell[@]}")  # 连接数组
echo "the newarray is ${linuxshell[@]}"  # 输出合并后的数组
echo "thelength of new array is ${#linuxshell[@]}"  # 输出新的数组的长度
```

### 加载文件内容到数组

&emsp;&emsp;用户可以将普通的文本文件的内容直接加载到数组中，文件的每一行构成数组一个元素的内容，在处理日志文件时非常有用。准备一个文本文件`demo.txt`，其内容为：

``` bash
Ubuntu
Suse
Fedora
UTS
```

将`demo.txt`内容加载到一个数组中，然后将数组内容打印处理：

``` bash
content=(`cat"demo.txt"`)  # 加载文件内容

for s in "${content[@]}"  # 通过循环输出数组内容
do
    echo "$s"
done
```