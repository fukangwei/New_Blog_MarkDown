---
title: ftplib模块
categories: Python语法
date: 2018-12-28 09:10:38
---
&emsp;&emsp;`ftplib`模块定义了`FTP`类，用来实现简单的`ftp`客户端。<!--more-->

### 下载和上传文件

&emsp;&emsp;下载和上传文件的代码如下：

``` python
from ftplib import FTP

def ftpconnect(host, username, password):
    ftp = FTP()
    ftp.connect(host, 21)  # 连接
    ftp.login(username, password)  # 登录服务器，如果匿名登录，则用空串代替
    return ftp

def downloadfile(ftp, remotepath, localpath):
    bufsize = 1024  # 设置缓冲块大小
    fp = open(localpath, 'wb')  # 以写模式在本地打开文件
    # 接收服务器上的文件，并写入本地文件
    ftp.retrbinary('RETR ' + remotepath, fp.write, bufsize)
    fp.close()  # 关闭文件

def uploadfile(ftp, remotepath, localpath):
    bufsize = 1024
    fp = open(localpath, 'rb')
    ftp.storbinary('STOR ' + remotepath, fp, bufsize)  # 上传文件
    fp.close()

if __name__ == "__main__":
    ftp = ftpconnect("******", "***", "***")
    downloadfile(ftp, "***", "***")
    uploadfile(ftp, "***", "***")
    ftp.quit()
```

### 其他命令

&emsp;&emsp;`ftp`相关的其他命令如下：

``` python
ftp.cwd(pathname)  # 设置FTP当前操作的路径
ftp.dir()  # 显示目录下所有文件信息
ftp.mkd(pathname)  # 新建远程目录
ftp.pwd()  # 返回当前所在位置
ftp.rmd(dirname)  # 删除远程目录
ftp.delete(filename)  # 删除远程文件
ftp.rename(fromname, toname)  # 修改名称
```