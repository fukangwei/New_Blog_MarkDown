---
title: Linux应用问题
categories: 软件与硬件问题
date: 2019-02-14 19:36:40
---
### command not found

#### insmod

&emsp;&emsp;在`Linux`环境下使用`insmod`时，一般会提示`command not found`。这是因为`insmod`命令是在`/sbin`目录下，而且也只有`root`将`/sbin`加到路径中，可以尝试用`/sbin/insmod`替代`insmod`。<!--more-->

#### yacc和lex

&emsp;&emsp;对于`yacc`，使用命令：

``` bash
sudo apt-get install byacc
```

&emsp;&emsp;对于`lex`，使用命令：

``` bash
sudo apt-get install flex
```

#### ifconfig

&emsp;&emsp;执行`export PATH="$PATH:/sbin`就可以了。

#### useradd

&emsp;&emsp;利用`su -`而不是`su`进入`root`，然后再输入`useradd username`。`su`只是取得`root`的权限，`su -`是取得`root`的权限后还执行`root`的`profile`来取得`root`环境变量。

---

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

&emsp;&emsp;使用如下命令解决：

``` cpp
sudo apt-get install libcurl4-openssl-dev
```

### tar not found in archive

&emsp;&emsp;下载了一个`eclipse`，想把它解压到`/usr`目录，却出现如下问题：

``` bash
$ tar -zxvf eclipse-jee-indigo-SR2-linux-gtk-x86_64.tar.gz /usr
tar: /usr: Not found in archive
tar: Exiting with failure status due to previous errors
```

原因是因为压缩文件时使用的是相对路径，在当前目录下找不到`/usr`目录，使用`-C`指定解压目录即可解决：

``` bash
tar -zxvf eclipse-jee-indigo-SR2-linux-gtk-x86_64.tar.gz -C /usr
```

### Agent admitted failure to sign using the key

&emsp;&emsp;`SSH`生成`id_rsa`、`id_rsa.pub`后，连接服务器却报`Agent admitted failure to sign using the key`。解决方法：在当前用户下执行命令`ssh-add`。

### 执行程序出现Text file busy

&emsp;&emsp;使用`fuser`命令查看程序文件被哪个进程占用，然后用`kill`命令杀死该进程，即解决问题。`fuser`命令是用来显示所有正在使用着指定的`file`、`file system`或者`socket`的进程信息：

``` bash
$ fuser <程序文件名>
<程序文件名>:         50340
$ kill -TERM 50340
```

注意，在普通用户下使用`fuser`是没有结果的，要切换至`root`用户。

### Windows压缩包在Linux下解压的乱码

&emsp;&emsp;有一个`Windows`下的代码压缩包，内文件夹或文件名含中文，放到`Linux`系统下解压，中文出现乱码。解决方法如下：
&emsp;&emsp;1. 安装`7zip`和`convmv`：

``` bash
sudo apt-get install p7zip-full convmv
```

&emsp;&emsp;2. 假设压缩包名为`abc.zip`，进入`abc.zip`所在路径，执行如下命令：

``` bash
LANG=C 7z x abc.zip
convmv -f cp936 -t utf8 -r --notest *
```

### 无法解析或打开软件包的列表或是状态文件

&emsp;&emsp;原因是软件包出错，解决方法如下：

``` bash
sudo rm /var/lib/apt/lists/* -vf
sudo apt-get update
```

### permission denied

&emsp;&emsp;执行安装命令`./install`时，遇到`permission denied，bash: ./install: Permission denied`的错误，即便在`root`模式下也是同样的问题。解决方法如下：

``` bash
# 解决方法一
chmod +x install
./install
# 解决方法二
bash ./install
```

`./configure`时遇到`permission denied`也是同样的方法。

### insmod模块无显示的解决方法

&emsp;&emsp;原因是它把输出信息写进系统日志文件里去了，可以去查看`var/log/message`，或者使用`dmesg`命令查看输出。我的方案是使用`dmesg | tail`，只看输出的后几行。或者查看`/var/log/syslog`文件，使用命令`tail /var/log/syslog`。

### /bin/bash^M: bad interpreter: 没有那个文件或目录

&emsp;&emsp;运行脚本时出现了这样一个错误，打开之后并没有找到所谓的`^M`。后来才知道原来是文件格式的问题，也就是`Linux`和`Windows`之间的不完全兼容。
&emsp;&emsp;错误分析：在`Windows`下编辑的脚本，可能有一些不可见字符。那些脚本文件是`DOS`格式的，即每一行的行尾以`\r\n`来标识，其`ASCII`码分别是`0x0D`和`0x0A`。
&emsp;&emsp;解决方法：使用`vim`打开文件，然后使用`set ff?`查看文件的格式，如果出现`fileformat=dos`，那么就基本可以确定是这个问题了。使用`set fileformat=unix`，然后保存文件即可。

### error while loading shared libraries

&emsp;&emsp;在`Linux`下运行程序时，出现了`error while loading shared libraries`这种错误。出现这类错误的原因：系统不知道`xxx.so`放在哪个目录下，这时就要在`/etc/ld.so.conf`中加入`xxx.so`所在的目录。
&emsp;&emsp;一般情况下，有很多的`.so`文件会存放在`/usr/local/lib`目录下，去该目录寻找，果然发现自己所需要的`.so`文件。所以在`/etc/ld.so.conf`中加入`/usr/local/lib`。保存之后，再运行`/sbin/ldconfig -v`更新一下配置即可。`/etc/ld.so.conf`的内容为：

``` bash
include /etc/ld.so.conf.d/*.conf /usr/local/lib
```

### 更换介质：请把标有...DVD的盘片插入驱动器"/media/cdrom/"再按回车键

&emsp;&emsp;在通过`apt-get install`安装软件时，可能会出现上述提示。
&emsp;&emsp;解决方法：使用`root`权限修改`/etc/apt/sources.list`文件，注释掉`deb cdrom:`开头的行，然后执行`apt-get update`。

### Lua: Error during loading: \[string "/usr/share/wireshark/init.lua"\]:45

&emsp;&emsp;运行`Wireshark`时，可能会报出上述错误。
&emsp;&emsp;解决方法：修改`/etc/wireshark/init.lua`文件，将倒数第二行的`dofile(DATA_DIR.."console.lua")`改为`--dofile(DATA_DIR.."console.lua")`。

### Startup Error: Unable to detect graphics environment

&emsp;&emsp;使用`Xshell`控制`Linux`系统安装软件时会出现这样的问题，在`Linux`自带的终端操作就可以了。

### E: Unable to correct problems, you have held broken packages

&emsp;&emsp;解决方法如下：

``` bash
sudo apt-get update && sudo apt-get upgrade
```

### 不支持IPv6

&emsp;&emsp;如果某些`Linux`系统(例如树莓派)不支持`IPv6`，可以这样修改：打开`/etc/modprobe.d/ipv6.conf`，使用`#`注释掉`alias net-pf-10 off`，开启`alias ipv6 on`，然后`reboot`。

### curses.h: No such file or directory

&emsp;&emsp;原因是少安装了`curses`库，解决方法如下：

``` bash
sudo apt-get install libncurses5-dev
```

### undefined reference to \`initscr'

&emsp;&emsp;代码如下：

``` cpp
#include <curses.h>

int main() {
    initscr();
    clear();
    move ( 10, 20 );
    addstr ( "Hello world" );
    move ( LINES - 1, 0 );
    refresh();
    getch();
    endwin();
    return 0;
}
```

在终端输入`gcc -o hello hello.c -Incurses`，发现如下错误：

``` cpp
/tmp/ccsmlmYV.o: In function `main':
hello.c:(.text+0xa): undefined reference to `initscr'
hello.c:(.text+0xf): undefined reference to `stdscr'
hello.c:(.text+0x17): undefined reference to `wclear'
hello.c:(.text+0x1c): undefined reference to `stdscr'
hello.c:(.text+0x34): undefined reference to `wmove'
hello.c:(.text+0x39): undefined reference to `stdscr'
hello.c:(.text+0x51): undefined reference to `waddnstr'
hello.c:(.text+0x56): undefined reference to `LINES'
hello.c:(.text+0x5e): undefined reference to `stdscr'
hello.c:(.text+0x72): undefined reference to `wmove'
hello.c:(.text+0x77): undefined reference to `stdscr'
hello.c:(.text+0x7f): undefined reference to `wrefresh'
hello.c:(.text+0x84): undefined reference to `stdscr'
hello.c:(.text+0x8c): undefined reference to `wgetch'
hello.c:(.text+0x91): undefined reference to `endwin'
collect2: ld returned 1 exit status
```

在终端输入`gcc hello.c -o hello -I/usr/include -L/usr/lib -lncurses`即可解决。

### E: 无法获得锁 /var/lib/apt/lists/lock - open (11: 资源暂时不可用)

&emsp;&emsp;重启系统就可以了，可能有系统的其他`apt-get`进程在运行中。

### Linux和Windows之间ping失败

&emsp;&emsp;`Linux`和`Windows`之间`ping`失败，可能是防火墙的原因。如果`Windows`系统对`Linux`系统`ping`操作失败，可以考虑关闭`Linux`的防火墙：

``` bash
service iptables stop
setenforce 0
```

如果`linux`系统对`Windows`系统`ping`失败，可以考虑关闭`Windows`的防火墙。

### WARNING: REMOTE HOST IDENTIFICATION HAS CHANGED

&emsp;&emsp;在使用`ssh`命令远程连接服务器，可能会出现上述问题。原因是第一次使用`ssh`连接时，服务器会生成一个认证，并储存在客户端(使用`ssh`程序的计算机)中的`known_hosts`。但是如果服务器验证失效了(例如重装服务器系统)，认证当然也需要更改，当服务器与客户端的验证不同时，就会报出上述错误。因此，只要把客户端中的认证删除，连线服务器时重新生成一个，就可以解决了。在客户端输入如下指令：

``` bash
ssh-keygen -R 服务器的IP
```

该解决方案对于`Windows`的`cmd`和`PowerShell`会失效，报出错误`Updating known_hosts is not supported in Windows yet`，建议使用`Linux`子系统的`bash`。

### conda command not found

&emsp;&emsp;修改用户目录下的`.bash_profile`文件(或者`.bashrc`文件)，输入如下内容：

``` bash
export PATH="~/anaconda/bin:$PATH"
```

然后使用命令`source ~/.bash_profile`。