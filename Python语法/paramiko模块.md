---
title: paramiko模块
date: 2018-12-28 09:38:34
categories: Python语法
---
&emsp;&emsp;`paramiko`是用`python`语言写的一个模块，遵循`SSH2`协议，支持以加密和认证的方式，进行远程服务器的连接，其安装命令如下：

``` bash
pip install paramiko
```

下面是两种使用`paramiko`连接到`linux`服务器的代码：

- 方式一如下：

``` python
ssh = paramiko.SSHClient()
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
ssh.connect("某IP地址", 22, "用户名", "口令")
```

- 方式二如下：

``` python
t = paramiko.Transport(("主机", "端口"))
t.connect(username="用户名", password="口令")
```

如果连接远程主机需要提供密钥，上面第二行代码可改成：

``` python
t.connect(username="用户名", password="口令", hostkey="密钥")
```

&emsp;&emsp;如果`Linux`服务器开放了`22`端口，在`Windows`端，我们可以使用`paramiko`远程连接到该服务器，并执行任意命令，然后通过`print`或其它方式得到执行结果：

``` python
import paramiko
​
ssh = paramiko.SSHClient()
ssh.set_missing_host_key_policy(paramiko.AutoAddPolicy())
ssh.connect("某IP地址", 22, "用户名", "口令")
stdin, stdout, stderr = ssh.exec_command("你的命令")
print(stdout.readlines())
ssh.close()
```

其中`你的命令`可以是任意`Linux`支持的命令。
&emsp;&emsp;从`Widnows`端下载`Linux`服务器上的文件：

``` python
import paramiko
​
t = paramiko.Transport(("主机", "端口"))
t.connect(username="用户名", password="口令")
sftp = paramiko.SFTPClient.from_transport(t)
remotepath = "/var/log/system.log"
localpath = "/tmp/system.log"
sftp.get(remotepath, localpath)
t.close()
```

&emsp;&emsp;从`Widnows`端上传文件到`Linux`服务器：

``` python
import paramiko
​
t = paramiko.Transport(("主机", "端口"))
t.connect(username="用户名", password="口令")
sftp = paramiko.SFTPClient.from_transport(t)
remotepath = "/var/log/system.log"
localpath = "/tmp/system.log"
sftp.put(localpath, remotepath)
t.close()
```

&emsp;&emsp;基于用户名和密码的`transport`方式登录：

``` python
import paramiko
​
trans = paramiko.Transport(('192.168.2.129', 22))  # 实例化一个transport对象
trans.connect(username='super', password='super')  # 建立连接
ssh = paramiko.SSHClient()  # 将sshclient对象的transport指定为以上的trans
ssh._transport = trans
stdin, stdout, stderr = ssh.exec_command('df -hl')  # 执行命令，和传统方法一样
print(stdout.read().decode())
trans.close()  # 关闭连接
```

---

### 关于SSHClient对象的exec_command

&emsp;&emsp;使用`SSHClient`对象的这个方法执行例如`ls`、`pwd`等命令的时候，结果都没有问题。但是执行`cd /path`就出现问题了，使用`pwd`发现还是在登录的时候的默认路径，即`/home/<user>`，相当于没有执行`cd`命令。这时就要把后续想要执行的命令和`cd`放在同一个字符串中，并传入`exec_command`：

``` python
client = paramiko.SSHClient()
client.set_missing_host_key_policy(paramiko.AutoAddPolicy())
client.connect('IP', username='username', password='password', timeout=5)
client.exec_command('cd /home/<user>/xxx/yyy; ls -al')
```

这时会列出`yyy`的文件列表。