---
title: Linux应用总结
categories: 软件与硬件问题
abbrlink: '99641364'
date: 2019-02-06 11:44:23
---
### 树莓派显示中文字体

&emsp;&emsp;使用如下命令：

``` bash
sudo apt-get update
sudo apt-get install ttf-wqy-microhei
sudo dpkg-reconfigure locales
```

使用空格键勾选`zhCN`的选项，使`zhCN.UTF-8`被选中，同时在`local`字库中选择`zh_CN.UTF-8`。

### ubuntu中文路径改为英文

&emsp;&emsp;使用如下命令：

``` bash
export LANG=en_US
xdg-user-dirs-gtk-update
```

跳出对话框询问是否将目录转化为英文路径，同意并关闭。最后在终端中输入命令：

``` bash
export LANG=zh_CN
```

### 挂载U盘

&emsp;&emsp;一定注意，挂载`U`盘需要有管理员的权限。先在`/mnt`目录下建立一个名为`USB`的文件夹，实际上文件夹名称是随意的。
&emsp;&emsp;然后将`U`盘插入电脑的`USB`接口，输入`fdisk -l`，查看一下磁盘分区的变化情况。一般会看到多出了一个名为`/dev/sdb1`的分区，这个就是刚才插入的`U`盘设备。当然在不同的系统环境中，显示的`U`盘设备名称有所不同。
&emsp;&emsp;现在开始挂载`U`盘设备，使用命令`mount -t vfat /dev/sdb1 /mnt/USB`。挂载成功后，可以使用`ls`命令查看`U`盘里的文件。
&emsp;&emsp;当完成对`U`盘的操作之后，需要使用命令`umount /mnt/USB`来卸载它。千万不能直接拔下`U`盘，否则有可能会对U盘造成损坏。

### ubuntu软件源更换

&emsp;&emsp;首先备份系统本身源文件：

``` bash
cp /etc/apt/sources.list /etc/apt/sources.list.backup
```

然后修改源文件内容，将新的源地址写入该文件中：

``` bash
gedit /etc/apt/sources.list
```

最后保存文件，并刷新配置：

``` bash
apt-get update
apt-get upgrade
```

### su初始密码设置

&emsp;&emsp;`Ubuntu`刚安装后，不能在`terminal`中运行`su`命令，因为`root`没有默认密码，需要手动设定。
&emsp;&emsp;打开一个`terminal`，输入下面的命令：

``` bash
sudo passwd [root]
```

回车后会提示让你输入原始密码、新密码和确认密码：

``` bash
[sudo] password for you:   # 输入你的密码(现在的密码)，不回显
Enter new UNIX password:   # 设置root密码
Retype new UNIX password:  # 重复这样输入
```

这样`root`的密码设置好了。
&emsp;&emsp;使用`su`命令就可以切换到`root`用户了。`su`和`sudo`的区别是：`su`的密码是`root`的密码，而`sudo`的密码是用户的密码；`su`直接将身份变成`root`，而`sudo`是用户登录后以`root`的身份运行命令，不需要知道`root`密码。