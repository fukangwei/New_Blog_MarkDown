---
title: samba服务器搭建
date: 2021-03-20 20:16:08
categories: 软件与硬件问题
---
&emsp;&emsp;使用`apt-get`安装`samba`服务器：<!--more-->

``` bash
sudo apt-get install samba
```

&emsp;&emsp;配置`smb.conf`文件：

``` bash
sudo vim /etc/samba/smb.conf
```

在`smb.conf`的最后加入以下配置，并保存退出：

``` bash
[work]
    comment = samba home directory
    path = /home/fuxinzi/
    public = yes
    browseable = yes
    public = yes
    writeable = yes
    read only = no
    valid users = fuxinzi
    create mask = 0777
    directory mask = 0777
    available = yes
```

&emsp;&emsp;配置`samba`服务器的登录密码：

``` bash
sudo smbpasswd -a fuxinzi
```

&emsp;&emsp;重新启动`samba`服务器：

``` bash
sudo service smbd restart
```

&emsp;&emsp;在`Windows`的地址栏上输入`\\samba服务器的IP地址`，即可将`linux`系统的文件夹挂载到`Windows`系统上。