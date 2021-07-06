---
title: paramiko模块
categories: Python语法
date: 2018-12-28 09:38:34
---
### 基础用法

&emsp;&emsp;`paramiko`用于远程连接服务器。<!--more-->
&emsp;&emsp;使用`paramiko`连接`linux`服务器的方法如下：

``` python
ssh = paramiko.SSHClient()
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
ssh.connect("某IP地址", 22, "用户名", "密码")
```

&emsp;&emsp;如果连接远程主机需要提供密钥，则方法如下：

``` python
t.connect(username="用户名", password="密码", hostkey="密钥")
```

&emsp;&emsp;代码实例：

``` python
import paramiko

trans = paramiko.Transport(('192.168.2.5', 22))  # 实例化一个transport对象
trans.connect(username='fuxinzi', password='171720')  # 建立连接
ssh = paramiko.SSHClient()
ssh._transport = trans
stdin, stdout, stderr = ssh.exec_command('ls -al')  # 执行命令
print(stdout.read().decode())
trans.close()  # 关闭连接
```

### 下载文件

&emsp;&emsp;从`Linux`服务器下载文件的方法如下：

``` python
import paramiko

t = paramiko.Transport(("主机", "端口"))
t.connect(username="用户名", password="密码")
sftp = paramiko.SFTPClient.from_transport(t)
remotepath = "/var/log/system.log"
localpath = "/tmp/system.log"
sftp.get(remotepath, localpath)
t.close()
```

### 上传文件

&emsp;&emsp;向`Linux`服务器上传文件的方法如下：

``` python
import paramiko

t = paramiko.Transport(("主机", "端口"))
t.connect(username="用户名", password="密码")
sftp = paramiko.SFTPClient.from_transport(t)
remotepath = "/var/log/system.log"
localpath = "/tmp/system.log"
sftp.put(localpath, remotepath)
t.close()
```

### exec_command

&emsp;&emsp;使用`SSHClient`执行`ls`、`pwd`等命令时，结果都没有问题。但是执行`cd /path`时，使用`pwd`发现还是在登录时的默认路径，即`/home/<user>`。
&emsp;&emsp;这时，就要把后续想要执行的命令和`cd`放在同一个字符串中，并传入`exec_command`：

``` python
client.exec_command('cd /home/<user>/xxx/yyy; ls -al')
```