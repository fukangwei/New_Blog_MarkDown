---
title: 安装SQLite
date: 2019-02-06 18:02:14
categories: 软件与硬件问题
---
### Linux下的安装

&emsp;&emsp;使用如下命令进行安装：

``` bash
sudo apt-get install sqlite sqlite3
```

执行命令`sqlite -version`，查看`sqlite`的版本。
&emsp;&emsp;使用如下命令，创建一个名为`test.db`的数据库：

``` sql
sqlite3 test.db
```

你可以输入`.help`命令查看帮助信息。
&emsp;&emsp;如下命令用于创建一个名字叫`mytabel`的数据表，该数据表定义了三个字段，分别为`id`、`name`、`age`：

``` sql
create table mytable(id, name, age);
```

&emsp;&emsp;执行如下命令，用于向数据表中插入数据：

``` sql
insert into mytable(id, name, age) values(1, "张三", "21");
insert into mytable(id, name, age) values(2, "李四", "23");
```

&emsp;&emsp;使用如下命令来查询数据表：

``` sql
select * from mytable;
```

执行结果：

``` sql
1|张三|21
2|李四|23
```

&emsp;&emsp;执行命令`.mode column`，设置为列显示模式：

``` sql
.mode column
select * from mytable;
```

执行结果：

``` sql
1 张三 21
2 李四 23
```

&emsp;&emsp;执行命令`.header on`，可以显示数据表的字段：

``` sql
.header on
select * from mytable;
```

执行结果：

``` sql
id    name  age
----- ----- -----
1     张三  21
2     李四  23
```

&emsp;&emsp;执行命令`.exit`，用于退出数据库。
&emsp;&emsp;使用命令`.databases`查看数据库信息，执行结果如下：

``` sql
seq name file
--- ---- -----------------
0   main /home/fkw/test.db
```

&emsp;&emsp;执行命令`.tables`，查看该数据库内的表信息，执行结果为`mytable`。
&emsp;&emsp;执行如下命令来安装可视化工具：

``` bash
sudo apt-get install sqlitebrowser
```

---

### Windows上安装SQLite

&emsp;&emsp;请访问`SQLite`下载页面(`http://www.sqlite.org/download.html`)，从`Windows`区下载预编译的二进制文件。需要下载`sqlite-shell-win32-*.zip`和`sqlite-dll-win32-*.zip`压缩文件。创建文件夹`C:\sqlite`，并在此文件夹下解压上面两个压缩文件，将得到`sqlite3.def`、`sqlite3.dll`和`sqlite3.exe`文件。添加`C:\sqlite`到`PATH`环境变量，最后在命令提示符下，使用`sqlite3`命令，将显示如下结果：

``` bash
C:\>sqlite3
SQLite version 3.7.15.2 2013-01-09 11:53:05
Enter ".help" for instructions
Enter SQL statements terminated with a ";"
sqlite>
```

---

### 嵌入式Linux移植sqlite3

&emsp;&emsp;下载`sqlite`最新版本，网址为`http://www.sqlite.org/download.html`。然后执行如下命令：

``` bash
tar xvzf sqlite-autoconf-3070900.tar.gz –C /opt/  # 解压源码包到“/opt”目录下
mkdir /opt/build  # 建立“make install”目录
cd /opt/sqlite-autoconf-3070900  # 进入解压出得文件夹中
./configure -host=arm-linux -prefix=/opt/build  # 执行configure命令，生成Makefile文件
make  # 生成Makefile文件后，执行make命令
make install  # 执行安装命令
```

完成之后可以发现，之前所建目录`/opt/build`下生成`bin`、`include`、`lib`、`share`四个目录(`/opt/build/`目录就是`arm`版本的`sqlite3`库文件位置)。主要用到的文件有`./bin/sqlite3`、`./include/sqlite3.h`以及`./lib/`下的库文件。`./include/sqlite3.h`定义了`sqlite`的`API`接口，它会在有关`sqlite`的`c`文件交叉编译时用到。`./lib/`文件夹下是有关`sqlite`的静态链接库和动态链接库，查看该文件夹：

``` bash
$ ls lib/
libsqlite3.a   libsqlite3.la   libsqlite3.so   libsqlite3.so.0   libsqlite3.so.0.8.6   pkgconfig
```

`libsqlite3.so`和`libsqlite3.so.0`都是`libsqlite3.so.0.8.6`的软链接文件。真正需要下载到开发板`/lib`目录下的动态库是`libsqlite3.so.0.8.6`。下载到开发板后，还需对它建立软链接文件，因为可执行程序寻找的动态链接库的名字为`libsqlite3.so.0`。

``` bash
ln -s /lib/libsqlite3.so.0.8.6 /lib/libsqlite3.so.0
```

至此，`sqlite`的移植工作已经完成。
&emsp;&emsp;`./bin`文件夹下的`sqlite3`是`sqlite`可执行应用程序，下载到开发板的`/bin`或者`/usr/bin`目录下，并添加文件可执行权限。在开发板上执行命令：

``` bash
chmod +x sqlite3
```

直接输入`sqlite3`命令：

``` bash
$ sqlite3
SQLite version 3.7.9 2011-11-01 00:52:41
Enter ".help" for instructions
Enter SQL statements terminated with a ";"
sqlite>
```

&emsp;&emsp;`test_sqlite.c`如下：

``` cpp
#include <stdio.h>
#include <sqlite3.h>
​
/* 执行到sqlite3_exec的回调函数 */
static int callback ( void *NotUsed, int argc, char **argv, char **azColName ) {
    int i;
​
    for ( i = 0; i < argc; i++ ) {
        printf ( "%s = %s\n", azColName[i], argv[i] ? argv[i] : "NULL" );
    }
​
    printf ( "\n" );
    return 0;
}
​
int main ( int argc, char **argv ) {
    sqlite3 *db; /* sqlite里最常用的是“sqlite3 *”类型 */
    char *zErrMsg = 0;
    int rc;
​
    if ( argc != 3 ) { /* 可执行程序有三个参数 */
        fprintf ( stderr, "Usage: %s DATABASE SQL-STATEMENT\n", argv[0] );
    }
​
    rc = sqlite3_open ( argv[1], &db );
​
    /* main第一个参数是可执行文件名，第二个参数是打开或创建的数据库文件名 */
    if ( rc ) {
        fprintf ( stderr, "Can't open database: %s\n", sqlite3_errmsg ( db ) );
        sqlite3_close ( db ); /* 打开或创建数据库失败就关闭数据库 */
    }
​
    rc = sqlite3_exec ( db, argv[2], callback, 0, &zErrMsg );
​
    /* main第三个参数argv[2]传入到上面这条执行sql语句，查询数据库 */
    if ( rc != SQLITE_OK ) { /* SQLITE_OK表示操作正常 */
        fprintf ( stderr, "SQL error: %s\n", zErrMsg );
    }
​
    sqlite3_close ( db ); /* 关闭数据库 */
    return 0;
}
```

动态编译命令为：

``` bash
arm-linux-gcc -o test test_sqlite.c -I /opt/build/include/ -L /opt/build/lib -lsqlite3
```

下载到开发板上执行(也要给`test`添加执行权限)：

``` bash
$ ./test xyz.db "create table tbl0(name varchar(10), number smallint);"
$ ./test xyz.db "insert into tbl0 values('cyc', 1);"
$ ./test xyz.db "insert into tbl0 values('dzy', 2);"
$ ./test xyz.db "select * from tbl0;"
name = cyc
number = 1
name = dzy
number = 2
```