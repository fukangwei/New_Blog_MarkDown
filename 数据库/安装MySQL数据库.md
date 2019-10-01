---
title: 安装MySQL数据库
abbrlink: 61cbe787
date: 2019-07-05 07:09:01
categories: 数据库
---
### ubuntu下安装MySQL数据库

&emsp;&emsp;`Ubuntu`上安装`MySQL`需要使用如下命令：

``` bash
sudo apt-get install mysql-server
sudo apt-get istnall mysql-client
sudo apt-get install libmysqlclient-dev
```

安装过程中会提示需要设置密码，一定不要忘记设置。安装完成之后，可以使用如下命令来检查是否安装成功：

``` bash
sudo netstat -tap | grep mysql
```

通过上述命令检查之后，如果看到有`MySQL`的`socket`处于`listen`状态，则表示安装成功。
&emsp;&emsp;登陆`MySQL`数据库可以通过如下命令：

``` bash
mysql -u root -p
```

`-u`表示选择登陆的用户名，`-p`表示登陆的用户密码。上面命令输入之后会提示输入密码，然后就可以登录到`MySQL`。通过`show databases;`可以查看当前`MySQL`服务器的数据库。

### Windows系统下安装MySQL数据库

&emsp;&emsp;`MySQL`安装文件分为两种，一种是`msi`格式，一种是`zip`格式。如果是`msi`格式的，可以直接点击安装，一般MySQL将会安装在`C:\Program Files\MySQL\MySQL Server 5.6`目录中；`zip`格式是需要用户自己解压的，解压缩之后`MySQL`就可以使用了。
&emsp;&emsp;完成上述步骤之后，很多用户开始使用`MySQL`，但是会出现错误，因为还没有配置环境变量。配置环境变量很简单，从`我的电脑 -> 属性 -> 高级 -> 环境变量`，选择`PATH`，在其后面添加`MySQL`的`bin`文件夹的路径，例如`C:\Program Files\MySQL\MySQL Server 5.6\bin`。
&emsp;&emsp;我们还需要修改一下配置文件，`MySQL`默认的配置文件是`C:\Program Files\MySQL\MySQL Server 5.6\my-default.ini`，其配置如下：

``` bash
basedir=C:\Program Files\MySQL\MySQL Server 5.6 (MySQL所在目录)
datadir=C:\Program Files\MySQL\MySQL Server 5.6\data (MySQL的data目录，需要在MySQL的根目录下新建)
```

&emsp;&emsp;以管理员身份运行`cmd`，输入`cd C:\Program Files\MySQL\MySQL Server 5.6\bin`进入`MySQL`的`bin`文件夹，再输入`mysqld -install`，安装成功。
&emsp;&emsp;安装成功后就要启动服务了，继续在`cmd`中输入`net start mysql`，服务启动成功！
&emsp;&emsp;服务启动成功之后，就可以登录了。输入`mysql -u root -p`。第一次登录没有密码，直接按回车过，登录成功！