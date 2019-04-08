---
title: gcc教程
date: 2019-01-18 09:42:36
tags:
---
### arm-linux-gcc的安装

&emsp;&emsp;首先解压安装文件：

``` bash
tar zxvf arm-linux-gcc-4.4.3.tar.gz -C /
```

`-C /`会让解压的文件自动放在根目录下的指定路径。执行该命令，将把`arm-linux-gcc`安装到`/opt/Friendlyarm/toolschain/4.4.3`目录。
&emsp;&emsp;建立安装目录，并复制文件：

``` bash
sudo mkdir /usr/local/arm
sudo cp -r /opt/FriendlyARM/toolschain/4.4.3 /usr/local/arm
```

&emsp;&emsp;添加环境变量，方法是修改`/etc/bash.bashrc`文件，在最后加上如下内容：

``` bash
export PATH=$PATH:/opt/FriendlyARM/toolschain/4.4.3/bin
```

输入如下命令使环境变量生效：

``` bash
source /etc/bash.bashrc
```

### arm-linux-gcc的用法

&emsp;&emsp;以文件`example.c`为例说明`arm-linux-gcc`的用法：

- `arm-linux-gcc -o example example.c`：不加`-c`、`-S`、`-E`参数，编译器将执行预处理、编译、汇编、连接操作直接生成可执行代码。`-o`参数用于指定输出的文件，输出文件名为`example`。如果不指定输出文件，则默认输出`a.out`。
- `arm-linux-gcc -c -o example.o example.c`：`-c`参数将对源程序`example.c`进行预处理、编译、汇编操作，生成`example.o`文件。去掉指定输出选项`-o example.o`自动输出为`example.o`，所以在这里`-o`加和不加都可以。
- `arm-linux-gcc -S -o example.s example.c`：`-S`参数将对源程序`example.c`进行预处理、编译，生成`example.s`文件。`-o`选项同上。
- `arm-linux-gcc -E -o example.i example.c`：`-E`参数将对源程序`example.c`进行预处理，生成`example.i`文件(不同版本不一样，有的将预处理后的内容打印到屏幕上)。就是将`include`、`define`等进行文件插入及宏扩展等操作。
- `arm-linux-gcc -v -o example example.c`：加上`-v`参数，显示编译时的详细信息、编译器的版本、编译过程等。
- `arm-linux-gcc -g -o example example.c`：`-g`选项，加入`GDB`能够使用的调试信息，使用`GDB`调试时比较方便。
- `arm-linux-gcc -Wall -o example example.c`：`-Wall`选项打开了所有需要注意的警告信息，像是在声明之前就使用的函数，声明后却没有使用的变量等。
- `arm-linux-gcc -Ox -o example example.c`：`-Ox`使用优化选项，`X`的值为`空`、`0`、`1`、`2`、`3`。`0`为不优化，优化的目的是减少代码空间和提高执行效率等，但相应的编译过程时间将较长并占用较大的内存空间。
- `arm-linux-gcc -I /home/include -o example example.c`：`-I dirname`将`dirname`所指出的目录加入到程序头文件目录列表中。如果在预设系统及当前目录中没有找到需要的文件，就到指定的`dirname`目录中去寻找。
- `arm-linux-gcc -L /home/lib -o example example.c`：`-L dirname`将`dirname`所指出的目录加入到库文件的目录列表中。在默认状态下，连接程序`ld`在系统的预设路径中(如`/usr/lib`)寻找所需要的库文件。这个选项告诉连接程序，首先到`-L`指定的目录中去寻找，然后再到系统预设路径中寻找。
- `arm-linux-gcc –static -o libexample.a example.c`：编译静态链接库文件，其库文件名为`example.a`。

### gcc(或g++)常用的选项

&emsp;&emsp;语言标准相关：

- `-ansi`：`ANSI`标准。
- `-std=c99`：`C99`标准。
- `-std=gnu89`：`ISO/IEC 9899:1990`以及`GNU`扩充。
- `-std=gnu99`：`ISO/IEC 9899:1999`以及`GNU`扩充。
- `-trigraphs`：支持`ISO C`三字符组。
- `-std=c++11`：支持`C++11`标准。

&emsp;&emsp;链接相关：

- `-llibrary`：进行链接时加载名为`library.a`的静态库。该静态库位于系统的预设目录，或者由`-L`选项确定的目录下。