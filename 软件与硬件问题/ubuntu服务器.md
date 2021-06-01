---
title: ubuntu服务器
categories: 软件与硬件问题
date: 2019-02-14 16:37:15
---
### FTP服务器

&emsp;&emsp;在`Linux`系统中，`ftp`服务器的全名叫`vsftpd`。我们需要利用相关命令来安装`ftp`服务器，然后在`vsftpd.conf`中进行相关配置。<!--more-->
&emsp;&emsp;1. 首先使用如下命令进行安装：

``` bash
sudo apt-get install vsftpd
```

安装完成后，输入`vsftpd -version`查看是否安装成功。
&emsp;&emsp;2. 新建一个用于`FTP`的工作目录：

``` bash
mkdir /home/ftp
```

&emsp;&emsp;3. 新建`FTP`用户，并设置密码以及工作目录，其中`ftpname`是创建的用户名：

``` bash
sudo useradd -d /home/ftp -s /bin/bash ftpname
```

为新建的用户设置密码：

``` bash
passwd ftpname
```

可以使用`cat /etc/passwd`查看当前系统用户。
&emsp;&emsp;4. 修改`vsftpd`配置文件，该文件位于`/etc`目录下，相关的配置如下：

``` bash
listen=YES                     # 服务器监听
anonymous_enable=NO            # 匿名访问允许。此选项很危险，默认不要开启
local_enable=YES               # 本地主机访问允许
write_enable=YES               # 写允许
# anon_upload_enable=YES       # 匿名上传允许
# anon_mkdir_write_enable=YES  # 匿名创建文件夹允许
dirmessage_enable=YES          # 进入文件夹允许
xferlog_enable=YES             # ftp日志记录允许
connect_from_port_20=YES       # 允许使用端口号20作为数据传送的端口
secure_chroot_dir=/var/run/vsftpd/empty
pam_service_name=vsftpd
rsa_cert_file=/etc/ssl/private/vsftpd.pem
local_root=/home/ftp           # 设置ftp的根目录为“/home/ftp”
```

设置好之后，保存退出，然后使用如下命令重启`ftp`服务器：

``` bash
sudo service vsftpd restart
```

&emsp;&emsp;5. 在浏览器中的地址栏输入`ftp://ftp服务器地址`，然后输入账号和密码即可实现登录。

&emsp;&emsp;**补充说明**：
&emsp;&emsp;1. `anonymous_enable=NO`必须要打开，不能注释。
&emsp;&emsp;2. `ftp`服务器的根目录可以是多级目录，如`/home/fukangwei/ftp`。
&emsp;&emsp;3. 要设置好`ftp`服务器的根目录权限，否则上传文件时会出现`553 Could not create file`错误。

---

### tftp服务器

&emsp;&emsp;1. 安装`xinetd`、`tftp-hpa`和`tftpd-hpa`(`tftp-hpa`是客户端，`tftpd-hpa`是服务器端)：

``` bash
sudo apt-get install xinetd
sudo apt-get install tftp-hpa tftpd-hpa
```

&emsp;&emsp;2. 配置`TFTP`服务器：

``` bash
sudo vim /etc/default/tftpd-hpa
```

将原来的内容改为：

``` bash
TFTP_USERNAME="tftp"
TFTP_ADDRESS="0.0.0.0:69"
TFTP_DIRECTORY="tftp根目录"  # 服务器目录，需要设置权限为777
TFTP_OPTIONS="-l -c -s"
```

&emsp;&emsp;3. 重新启动`TFTP`服务：

``` bash
sudo service tftpd-hpa restart
```


---

### ssh服务器

&emsp;&emsp;1. 安装`ssh-server`和`ssh-client`：

``` bash
sudo apt-get install openssh-server
sudo apt-get install openssh-client
```

&emsp;&emsp;2. 确认`sshserver`是否安装好：

``` bash
ps -e | grep sshd
```

如果看到`sshd`，那说明`ssh-server`已经启动了；如果只有`ssh-agent`，说明`ssh-server`还没有启动，需要执行如下命令：

``` bash
sudo /etc/init.d/ssh start
```

&emsp;&emsp;3. `SSH`默认服务端口为`22`，用户可以自定义成其他端口，需要修改的配置文件为`/etc/ssh/sshd_config`，把里面的`Port`参数修改成其他数组即可，然后重启`SSH`服务：

``` bash
sudo /etc/init.d/ssh restart
```


---

### VNC服务器

&emsp;&emsp;在`ubuntu`上安装并运行`VNC`服务器，推荐使用`tightvnc`：

``` bash
sudo apt-get update
sudo apt-get install tightvncserver
```

安装完成之后，使用如下命令运行`tightvnc`服务器：

``` bash
vncserver :1
```

第一次运行时需要你设置一个密码，该密码和系统用户密码无关。
&emsp;&emsp;在`Windows`上安装`vnc viewer`，下载地址为`http://www.realvnc.com/`。启动`vnc viewer`，在`VNC Server`上填入`VNC服务器IP地址:1`，点击`Connect`，输入`vnc`密码即可。

---

### sftp服务器

&emsp;&emsp;`sftp`服务器使用如下命令进行安装：

``` bash
sudo apt-get install openssh-server
```

&emsp;&emsp;`sftp`是`Secure File Transfer Protocol`的缩写，意思是安全文件传送协议，可以为传输文件提供一种安全的加密方法，与`ftp`有着几乎一样的语法和功能。`SFTP`本身没有单独的守护进程，它必须使用`sshd`守护进程(端口号默认是`22`)来完成相应的连接操作。所以从某种意义上来说，`SFTP`并不像一个服务器程序，而更像是一个客户端程序。`SFTP`使用加密传输认证信息和数据，所以是非常安全的。但由于这种传输方式使用了加密和解密技术，所以传输效率比普通的`FTP`要低得多。常用的命令如下：

``` bash
sftp user@host               # 登陆远程主机
lcd或lpwd                    # 针对本机的命令都加上“l”
put filename.txt directory   # 将本机文件上传到远程
mput *.*                     # 将当前文件夹下的文件上传到远程
get filename.file directory  # 下载远程文件到本地
mget *.* directory           # 下载目录下所有远程文件到本地
?                            # 帮助
bye、exit或quit              # 退出
```