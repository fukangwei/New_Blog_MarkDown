---
title: 安装MySQL数据库
date: 2019-07-05 07:09:01
tags:
---
### ubuntu下安装MySql数据库

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

   MySQL安装文件分为两种，一种是msi格式的，一种是zip格式的。如果是msi格式的，可以直接点击安装，按照它给出的安装提示进行安装，一般MySQL将会安装在“C:\Program Files\MySQL\MySQL Server 5.6”目录中；zip格式是需要用户自己解压的，解压缩之后其实MySQL就可以使用了，但是需要进行配置。
   解压之后可以将该文件夹改名，放到合适的位置，个人建议把文件夹改名为“MySQL Server 5.6”，放到“C:\Program Files\MySQL”路径中。当然你也可以放到自己想放的任意位置。
   完成上述步骤之后，很多用户开始使用MySQL，但是会出现错误，因为还没有配置环境变量。配置环境变量很简单，从“我的电脑”->“属性”->“高级”->“环境变量”，选择PATH，在其后面添加mysql的bin文件夹的路径，例如“C:\Program Files\MySQL\MySQL Server 5.6\bin”，PATH为“…;C:\Program Files\MySQL\MySQL Server 5.6\bin ”，注意是追加，不是覆盖。
   我们还需要修改一下配置文件，mysql默认的配置文件是“C:\Program Files\MySQL\MySQL Server 5.6\my-default.ini”，其配置如下所示：
basedir=C:\Program Files\MySQL\MySQL Server 5.6 (mysql所在目录)
datadir=C:\Program Files\MySQL\MySQL Server 5.6\data （mysql的data目录，需要在MySQL的根目录下新建）
   以管理员身份运行cmd，输入“cd C:\Program Files\MySQL\MySQL Server 5.6\bin”进入mysql的bin文件夹，输入“mysqld -install”，安装成功。

   安装成功后就要启动服务了，继续在cmd中输入“net start mysql”，服务启动成功！

   服务启动成功之后，就可以登录了，输入“mysql -u root -p”。第一次登录没有密码，直接按回车过，登录成功！
