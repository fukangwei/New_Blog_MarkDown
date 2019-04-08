---
title: ftplib模块
date: 2018-12-28 09:10:38
categories: Python语法
---
&emsp;&emsp;`Python`中默认安装的`ftplib`模块定义了`FTP`类，可用来实现简单的`ftp`客户端，用于上传或下载文件。代码示例如下所示：

``` python
from ftplib import FTP  # 加载ftp模块
​
ftp = FTP()  # 设置变量
ftp.set_debuglevel(2)  # 打开调试级别2，显示详细信息
ftp.connect("IP", "port")  # 连接的“ftp sever”和端口
ftp.login("user", "password")  # 连接的用户名和密码
print(ftp.getwelcome())  # 打印出欢迎信息
ftp.cmd("xxx/xxx")  # 进入远程目录
bufsize = 1024  # 设置的缓冲区大小
filename = "filename.txt"  # 需要下载的文件
file_handle = open(filename, "wb").write  # 以写模式在本地打开文件
ftp.retrbinaly("RETR filename.txt", file_handle, bufsize)  # 接收服务器上文件并写入本地文件
ftp.set_debuglevel(0)  # 关闭调试模式
ftp.quit()  # 退出ftp
```

&emsp;&emsp;`ftp`相关命令操作：

``` python
ftp.cwd(pathname)  # 设置FTP当前操作的路径
ftp.dir()  # 显示目录下所有目录信息
ftp.nlst()  # 获取目录下的文件
ftp.mkd(pathname)  # 新建远程目录
ftp.pwd()  # 返回当前所在位置
ftp.rmd(dirname)  # 删除远程目录
ftp.delete(filename)  # 删除远程文件
ftp.rename(fromname, toname)  # 将fromname修改名称为toname
ftp.storbinaly("STOR filename.txt", file_handel, bufsize)  # 上传目标文件
ftp.retrbinary("RETR filename.txt", file_handel, bufsize)  # 下载FTP文件
```

&emsp;&emsp;`FTP.quit`与`FTP.close`的区别如下所示：

- `FTP.quit`：发送`QUIT`命令给服务器并关闭掉连接。这是一个比较缓和的关闭连接方式，但如果服务器对`QUIT`命令返回错误，则会抛出异常。
- `FTP.close`：单方面的关闭掉连接，不应该用在已经关闭的连接之后，例如不应该用在`FTP.quit`之后。

&emsp;&emsp;下载和上传文件的代码如下所示：

``` python
from ftplib import FTP
​
def ftpconnect(host, username, password):
    ftp = FTP()
    # ftp.set_debuglevel(2)  # 打开调试级别2，显示详细信息
    ftp.connect(host, 21)  # 连接
    ftp.login(username, password)  # 登录服务器，如果匿名登录，则用空串代替即可
    return ftp
​
def downloadfile(ftp, remotepath, localpath):
    bufsize = 1024  # 设置缓冲块大小
    fp = open(localpath, 'wb')  # 以写模式在本地打开文件
    # 接收服务器上的文件，并写入本地文件
    ftp.retrbinary('RETR ' + remotepath, fp.write, bufsize)
    # ftp.set_debuglevel(0)  # 关闭调试
    fp.close()  # 关闭文件
​
def uploadfile(ftp, remotepath, localpath):
    bufsize = 1024
    fp = open(localpath, 'rb')
    ftp.storbinary('STOR ' + remotepath, fp, bufsize)  # 上传文件
    # ftp.set_debuglevel(0)
    fp.close()
​
if __name__ == "__main__":
    ftp = ftpconnect("******", "***", "***")
    downloadfile(ftp, "***", "***")
    uploadfile(ftp, "***", "***")
    ftp.quit()
```