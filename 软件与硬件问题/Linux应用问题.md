---
title: Linux应用问题
categories: 软件与硬件问题
date: 2019-02-14 19:36:40
---
### command not found

#### yacc和lex

&emsp;&emsp;对于`yacc`，使用命令：<!--more-->

``` bash
sudo apt-get install byacc
```

&emsp;&emsp;对于`lex`，使用命令：

``` bash
sudo apt-get install flex
```

#### ifconfig

&emsp;&emsp;执行`export PATH="$PATH:/sbin`即可。

### stdio.h: No such file or directory

&emsp;&emsp;解决方法如下：

``` cpp
sudo apt-get install libc6-dev
```

### stdint.h: No such file or directory

&emsp;&emsp;解决方法如下：

``` bash
sudo apt-get install libnewlib-arm-none-eabi
```

### 找不到curl/curl.h

&emsp;&emsp;解决方法如下：

``` cpp
sudo apt-get install libcurl4-openssl-dev
```

### Agent admitted failure to sign using the key

&emsp;&emsp;`SSH`生成`id_rsa`、`id_rsa.pub`后，连接服务器却报错`Agent admitted failure to sign using the key`。
&emsp;&emsp;解决方法：在当前用户下执行命令`ssh-add`。

### Text file busy

&emsp;&emsp;使用`fuser`查看程序文件被哪个进程占用，然后用`kill`杀死该进程：

``` bash
$ fuser <程序文件名>
<程序文件名>:         50340
$ kill -TERM 50340
```

### 无法解析或打开软件包的列表或是状态文件

&emsp;&emsp;解决方法如下：

``` bash
sudo rm /var/lib/apt/lists/* -vf
sudo apt-get update
```

### /bin/bash^M: bad interpreter

&emsp;&emsp;解决方法如下：

``` bash
dos2unix filename
```

### 更换介质：请把标有...DVD的盘片插入驱动器&#34;/media/cdrom/&#34;再按回车键

&emsp;&emsp;解决方法如下：

1. 使用`root`权限修改`/etc/apt/sources.list`。
2. 注释掉`deb cdrom:`开头的行。
3. 执行`apt-get update`。

### Lua: Error during loading: \[string &#34;/usr/share/wireshark/init.lua&#34;\]:45

&emsp;&emsp;解决方法如下：

1. 打开`/etc/wireshark/init.lua`。
2. 将`dofile(DATA_DIR.."console.lua")`改为`--dofile(DATA_DIR.."console.lua")`。
3. 重新启动`wireshark`。

### Startup Error: Unable to detect graphics environment

&emsp;&emsp;使用`Xshell`远程控制`Linux`系统安装软件时，可能会出现这样的问题。
&emsp;&emsp;解决方法：在`Linux`自带的终端上操作就可以了。

### E: Unable to correct problems, you have held broken packages

&emsp;&emsp;解决方法如下：

``` bash
sudo apt-get update
sudo apt-get upgrade
```

### 不支持IPv6

&emsp;&emsp;解决方法如下：

1. 打开`/etc/modprobe.d/ipv6.conf`。
2. 使用`#`注释掉`alias net-pf-10 off`。
3. 开启`alias ipv6 on`
4. 最后`reboot`系统。

### curses.h: No such file or directory

&emsp;&emsp;解决方法如下：

``` bash
sudo apt-get install libncurses5-dev
```

### E: 无法获得锁 /var/lib/apt/lists/lock

&emsp;&emsp;可能系统中有其他的`apt-get`进程正在运行中。

### Linux和Windows之间ping失败

&emsp;&emsp;如果`Windows`系统对`Linux`系统`ping`操作失败，可以考虑关闭`Linux`的防火墙：

``` bash
service iptables stop
setenforce 0
```

&emsp;&emsp;如果`Linux`系统对`Windows`系统`ping`失败，可以考虑关闭`Windows`的防火墙。

### WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED

&emsp;&emsp;解决方法是在客户端输入如下指令：

``` bash
ssh-keygen -R 服务器的IP
```

&emsp;&emsp;该解决方案对于`Windows`的`cmd`会失效，报出错误`Updating known_hosts is not supported in Windows yet`，建议使用`Linux`子系统的`bash`。

### conda command not found

&emsp;&emsp;解决方法如下：

1. 打开用户目录下的`.bashrc`。
2. 加上`export PATH="~/anaconda/bin:$PATH"`。
3. 使用命令`source ~/.bashrc`。