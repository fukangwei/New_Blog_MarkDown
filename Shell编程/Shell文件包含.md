---
title: Shell文件包含
date: 2019-01-22 08:22:03
categories: Shell编程
---
&emsp;&emsp;`Shell`可以包含外部脚本，这样可以很方便地封装一些公用的代码。`Shell`文件包含的语法格式如下：

``` bash
. filename  # 注意点号和文件名中间有一空格
source filename  # 第二种写法
```

&emsp;&emsp;创建两个`shell`脚本文件，`test1.sh`代码如下：

``` bash
#!/bin/bash
url="http://www.runoob.com"
```

&emsp;&emsp;`test2.sh`代码如下：

``` bash
#!/bin/bash
. ./test1.sh  # 第二种方法是“source ./test1.sh”
echo "菜鸟教程官网地址：$url"
```

接下来为`test2.sh`添加可执行权限并执行：

``` bash
$ chmod +x test2.sh
$ ./test2.sh
菜鸟教程官网地址：http://www.runoob.com
```

注意，被包含的文件`test1.sh`不需要可执行权限。