---
title: comm命令
categories: Shell编程
date: 2019-01-19 22:05:44
---
&emsp;&emsp;`comm`命令可以用于两个文件之间的比较，它有一些选项可以用来调整输出，以便执行交集、求差、以及差集操作。<!--more-->

- `交集`：打印出两个文件所共有的行。
- `求差`：打印出指定文件所包含的且不相同的行。
- `差集`：打印出包含在一个文件中，但不包含在其他指定文件中的行。

``` cpp
comm (选项) (参数)
```

选项 | 说明
-----|-----
`-1` | 不显示在第一个文件出现的内容
`-2` | 不显示在第二个文件中出现的内容
`-3` | 不显示同时在两个文件中都出现的内容

参数文件`1`为指定要比较的第一个有序文件，文件`2`为指定要比较的第二个有序文件。

``` bash
$ cat aaa.txt
aaa
bbb
ccc
ddd
eee
111
222
$ cat bbb.txt
bbb
ccc
aaa
hhh
ttt
jjj
$ comm aaa.txt bbb.txt
aaa
              bbb
              ccc
       aaa
ddd
eee
111
222
       hhh
       ttt
       jjj
第一列 第二列 第三列
```

输出的第一列只包含在`aaa.txt`中出现的行，第二列包含在`bbb.txt`中出现的行，第三列包含在`aaa.txt`和`bbb.txt`中相同的行。各列是以制表符`\t`作为定界符。
&emsp;&emsp;打印两个文件的交集，需要删除第一列和第二列：

``` bash
$ comm aaa.txt bbb.txt -1 -2
bbb
ccc
```

&emsp;&emsp;打印出两个文件中不相同的行，需要删除第三列：

``` bash
$ comm aaa.txt bbb.txt -3 | sed 's/^\t//'
aaa
aaa
ddd
eee
111
222
hhh
ttt
jjj
```

`sed 's/^\t//'`是将制表符删除，以便把两列合并成一列。
&emsp;&emsp;通过删除不需要的列，可以得到`aaa.txt`和`bbb.txt`的差集：

``` bash
$ comm aaa.txt bbb.txt -2 -3  # aaa.txt的差集
aaa
ddd
eee
111
222
$ comm aaa.txt bbb.txt -1 -3  # bbb.txt的差集
aaa
hhh
ttt
jjj
```