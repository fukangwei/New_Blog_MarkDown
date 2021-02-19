---
title: MySQL入门教程
date: 2020-04-03 20:39:42
categories: 数据库
---
### MySQL入门教程

&emsp;&emsp;`MySQL`是最流行的关系型数据库管理系统，在`Web`应用方面，`MySQL`是最好的`RDBMS`(`Relational Database Management System`，关系数据库管理系统)应用软件之一。<!--more-->

### 什么是数据库？

&emsp;&emsp;数据库(`Database`)是按照数据结构来组织、存储和管理数据的仓库，每个数据库都有一个或多个不同的`API`用于创建、访问、管理、搜索和复制所保存的数据。我们也可以将数据存储在文件中，但是在文件中读写数据速度相对较慢。所以，现在我们使用关系型数据库管理系统来存储和管理的大数据量。
&emsp;&emsp;所谓的`关系型数据库`，是建立在关系模型基础上的数据库，借助于集合代数等数学概念和方法来处理数据库中的数据。关系数据库管理系统的特点如下：

- 数据以表格的形式出现
- 每行为各种记录名称
- 每列为记录名称所对应的数据域
- 许多的行和列组成一张表单
- 若干的表单组成`database`

### RDBMS术语

&emsp;&emsp;在学习`MySQL`数据库前，先了解`RDBMS`的一些术语：

- **数据库**：数据库是一些关联表的集合。
- **数据表**：表是数据的矩阵。在一个数据库中的表看起来像一个简单的电子表格。
- **列**：一列(数据元素)包含了相同的数据，例如邮政编码的数据。
- **行**：一行是一组相关的数据，例如一条用户订阅的数据。
- **冗余**：存储两倍数据，冗余可以使系统速度更快。
- **主键**：主键是唯一的，一个数据表中只能包含一个主键，你可以使用主键来查询数据。
- **外键**：外键用于关联两个表。
- **复合键**：复合键(组合键)将多个列作为一个索引键，一般用于复合索引。
- **索引**：使用索引可快速访问数据库表中的特定信息。索引是对数据库表中一列或多列的值进行排序的一种结构，类似于书籍的目录。
- **参照完整性**：参照的完整性要求关系中不允许引用不存在的实体。与`实体完整性`是关系模型必须满足的完整性约束条件，目的是保证数据的一致性。

### MySQL数据库

&emsp;&emsp;`MySQL`是一种关联数据库管理系统，关联数据库将数据保存在不同的表中，而不是将所有数据放在一个大仓库内，这样就增加了速度并提高了灵活性。其特点如下：

- `MySQL`是开源的，所以你不需要支付额外的费用。
- `MySQL`使用标准的`SQL`数据语言形式。
- `MySQL`可以允许于多个系统上，并且支持多种语言。这些编程语言包括`C`、`C++`、`Python`、`Java`、`Perl`、`PHP`、`Eiffel`、`Ruby`和`Tcl`等。
- `MySQL`支持大型数据库，支持`5000`万条记录的数据仓库，`32`位系统表文件最大可支持`4GB`，`64`位系统支持最大的表文件为`8TB`。
- `MySQL`是可以定制的，采用了`GPL`协议，你可以修改源码来开发自己的`MySQL`系统。

### MySQL安装

#### 使用`MySQL Client`(`MySQL`客户端)执行简单的`SQL`命令

&emsp;&emsp;你可以在`MySQL Client`(`MySQL`客户端)使用`MySQL`命令连接到`MySQL`服务器上，默认情况下`MySQL`服务器的密码为空，所以本实例不需要输入密码：

``` shell
$ mysql
```

以上命令执行后，会输出`mysql>`提示符，这说明你已经成功连接到`MySQL`服务器上。你可以在`mysql>`提示符执行`SQL`命令：

``` shell
mysql> SHOW DATABASES;
+----------+
| Database |
+----------+
| mysql    |
| test     |
+----------+
2 rows in set (0.13 sec)
```

#### MySQL安装后需要做的

&emsp;&emsp;`MySQL`安装成功后，默认的`root`用户密码为空，你可以使用以下命令来创建`root`用户的密码：

``` shell
$ mysqladmin -u root password "new_password";
```

现在你可以通过以下命令来连接到`MySQL`服务器：

``` shell
$ mysql -u root -p
Enter password: *******
```

在输入密码时，密码是不会显示了，你正确输入即可。

#### Linux系统启动时启动MySQL

&emsp;&emsp;如果想在`Linux`系统启动时启动`MySQL`服务器，你需要在`/etc/rc.local`文件中添加以下命令：

``` shell
/etc/init.d/mysqld start
```

同样，你需要将`mysqld`二进制文件添加到`/etc/init.d/`目录中。

### MySQL管理

#### 启动及关闭MySQL服务器

&emsp;&emsp;首先，我们需要通过以下命令来检查`MySQL`服务器是否启动：

``` shell
ps -ef | grep mysqld
```

如果`MySQL`已经启动，以上命令将输出`MySQL`进程列表；如果`MySQL`未启动，你可以使用以下命令来启动`MySQL`服务器：

``` shell
$ cd /usr/bin
$ ./safe_mysqld &
```

如果你想关闭目前运行的`MySQL`服务器，可以执行以下命令：

``` shell
$ cd /usr/bin
$ ./mysqladmin -u root -p shutdown
Enter password: ******
```

### MySQL连接

#### 使用MySQL二进制方式连接

&emsp;&emsp;你可以进入到`MySQL`命令提示符下来连接`MySQL`数据库。以下是从命令行中连接`MySQL`服务器的简单实例：

``` shell
$ mysql -u root -p
Enter password: ******
```

在登录成功后会出现`mysql>`命令提示窗口，你可以在上面执行任何`SQL`语句。以上命令执行后，登录成功的输出结果如下：

``` shell
Welcome to the MySQL monitor. Commands end with ; or \g.
Your MySQL connection id is 2854760 to server version: 5.0.9

Type 'help;' or '\h' for help. Type '\c' to clear the buffer.
```

在以上实例中，我们使用了`root`用户登录到`MySQL`服务器，当然你也可以使用其他`MySQL`用户登录。如果用户权限足够，任何用户都可以在`MySQL`的命令提示窗口中进行`SQL`操作。
&emsp;&emsp;退出`mysql>`命令提示窗口可以使用`exit`命令：

``` shell
mysql> exit
Bye
```

### MySQL操作数据库

#### 使用mysqladmin创建数据库

&emsp;&emsp;使用普通用户，你可能需要特定的权限来创建或者删除`MySQL`数据库，所以我们这边使用`root`用户登录。
&emsp;&emsp;可以使用`mysql`的`mysqladmin`命令来创建数据库。以下命令简单地演示了创建数据库的过程，数据库名为`W3CSCHOOL`：

``` shell
$ mysqladmin -u root -p create W3CSCHOOL
Enter password: ******
```

以上命令执行成功后，会创建`MySQL`数据库`W3CSCHOOL`。

#### 使用mysqladmin删除数据库

&emsp;&emsp;在删除数据库过程中，务必要十分谨慎，因为在执行删除命令后，所有数据将会消失。以下实例删除数据库`W3CSCHOOL`：

``` shell
$ mysqladmin -u root -p drop W3CSCHOOL
Enter password: ******
```

执行以上命令后，会出现一个提示框，来确认是否真的删除数据库：

``` shell
Dropping the database is potentially a very bad thing to do.
Any data stored in the database will be destroyed.

Do you really want to drop the 'W3CSCHOOL' database [y/N] y
Database "W3CSCHOOL" dropped
```

#### MySQL选择数据库

&emsp;&emsp;在你连接到`MySQL`数据库后，可能有多个可以操作的数据库，所以你需要选择你要操作的数据库。
&emsp;&emsp;在`mysql>`提示窗口中可以很简单地选择特定的数据库，你可以使用`SQL`命令来选择指定的数据库。以下实例选取了数据库`W3CSCHOOL`：

``` shell
mysql> use W3CSCHOOL;
Database changed
```

执行以上命令后，你就已经成功选择了`W3CSCHOOL`数据库，在后续的操作中都会在`W3CSCHOOL`数据库中执行。
&emsp;&emsp;注意，所有的数据库名、表名、表字段都是区分大小写的，所以你在使用`SQL`命令时需要输入正确的名称。

### MySQL数据类型

&emsp;&emsp;`MySQL`中定义数据字段的类型对数据库的优化是非常重要的。`MySQL`支持多种类型，大致可以分为`3`类：

1. 数值
2. 日期/时间
3. 字符串(字符)

#### 数值类型

&emsp;&emsp;`MySQL`支持所有标准`SQL`数值数据类型。这些类型包括严格数值数据类型(`INTEGER`、`SMALLINT`、`DECIMAL`和`NUMERIC`)，以及近似数值数据类型(`FLOAT`、`REAL`和`DOUBLE PRECISION`)。
&emsp;&emsp;关键字`INT`是`INTEGER`的同义词，关键字`DEC`是`DECIMAL`的同义词。`BIT`数据类型保存位字段值，并且支持`MyISAM`、`MEMORY`、`InnoDB`和`BDB`表。
&emsp;&emsp;作为`SQL`标准的扩展，`MySQL`也支持整数类型`TINYINT`、`MEDIUMINT`和`BIGINT`。

类型             | 大小                                                    | 用途
-----------------|---------------------------------------------------------|----------
`TINYINT`        | `1`字节                                                 | 小整数值
`SMALLINT`       | `2`字节                                                 | 大整数值
`MEDIUMINT`      | `3`字节                                                 | 大整数值
`INT`或`INTEGER` | `4`字节                                                 | 大整数值
`BIGINT`         | `8`字节                                                 | 极大整数值
`FLOAT`          | `4`字节                                                 | 单精度浮点数值
`DOUBLE`         | `8`字节                                                 | 双精度浮点数值
`DECIMAL`        | 对`DECIMAL(M, D)`，如果`M > D`，为`M + 2`，否则为`D + 2` | 小数值

#### 日期和时间类型

&emsp;&emsp;表示时间值的日期和时间类型为`DATETIME`、`DATE`、`TIMESTAMP`、`TIME`和`YEAR`。每个时间类型有一个有效值范围和一个`零`值，当指定不合法的、`MySQL`不能表示的值时，使用`零`值。
&emsp;&emsp;`TIMESTAMP`类型有专有的自动更新特性。

类型        | 大小     | 范围                                       | 格式                   | 用途
------------|---------|--------------------------------------------|-----------------------|--------
`DATE`      | `3`字节 | `1000-01-01`至`9999-12-31`                   | `YYYY-MM-DD`          | 日期值
`TIME`      | `3`字节 | `-838:59:59`至`838:59:59`                    | `HH:MM:SS`            | 时间值或持续时间
`YEAR`      | `1`字节 | `1901`至`2155`                               | `YYYY`                | 年份值
`DATETIME`  | `8`字节 | `1000-01-01 00:00:00`至`9999-12-31 23:59:59` | `YYYY-MM-DD HH:MM:SS` | 混合日期和时间值
`TIMESTAMP` | `8`字节 | `1970-01-01 00:00:00`至`2037年某时`           | `YYYYMMDD HHMMSS`     | 混合日期和时间值，时间戳

#### 字符串类型

&emsp;&emsp;字符串类型指`CHAR`、`VARCHAR`、`BINARY`、`VARBINARY`、`BLOB`、`TEXT`、`ENUM`和`SET`。

类型         | 大小               | 用途
-------------|-------------------|------
`CHAR`       | `0-255`字节        | 定长字符串
`VARCHAR`    | `0-65535`字节      | 变长字符串
`TINYBLOB`   | `0-255`字节        | 不超过`255`个字符的二进制字符串
`TINYTEXT`   | `0-255`字节        | 短文本字符串
`BLOB`       | `0-65535`字节      | 二进制形式的长文本数据
`TEXT`       | `0-65535`字节      | 长文本数据
`MEDIUMBLOB` | `0-16777215`字节   | 二进制形式的中等长度文本数据
`MEDIUMTEXT` | `0-16777215`字节   | 中等长度文本数据
`LOGNGBLOB`  | `0-4294967295`字节 | 二进制形式的极大文本数据
`LONGTEXT`   | `0-4294967295`字节 | 极大文本数据

### MySQL创建和删除数据表

&emsp;&emsp;创建`MySQL`数据表需要以下信息：

- 表名
- 表字段名
- 定义每个表字段

&emsp;&emsp;以下为创建`MySQL`数据表的`SQL`通用语法：

``` sql
CREATE TABLE table_name (column_name column_type);
```

以下例子我们将在`W3CSCHOOL`数据库中创建数据表`w3cschool_tbl`，注意`MySQL`命令终止符为分号`;`：

``` sql
mysql> CREATE TABLE w3cschool_tbl (
    -> w3cschool_id INT NOT NULL AUTO_INCREMENT,
    -> w3cschool_title VARCHAR(100) NOT NULL,
    -> w3cschool_author VARCHAR(40) NOT NULL,
    -> submission_date DATE,
    -> PRIMARY KEY (w3cschool_id)
    -> );
Query OK, 0 rows affected (0.16 sec)
```

- 如果你不想字段为`NULL`，可以设置字段的属性为`NOT NULL`。在操作数据库时，如果输入该字段的数据为`NULL`，就会报错。
- `AUTO_INCREMENT`定义列为自增的属性，一般用于主键，数值会自动加`1`。
- `PRIMARY KEY`关键字用于定义列为主键。

&emsp;&emsp;`MySQL`中删除数据表是非常容易的，但是你在进行删除表操作时要非常小心，因为执行删除命令后所有数据都会消失。以下为删除`MySQL`数据表的通用语法：

``` sql
DROP TABLE table_name;
```

以下实例删除了数据表`w3cschool_tbl`：

``` sql
mysql> DROP TABLE w3cschool_tbl
Query OK, 0 rows affected (0.8 sec)
```

### MySQL插入数据

&emsp;&emsp;`MySQL`表中使用`INSERT INTO SQL`语句来插入数据。以下为向`MySQL`数据表插入数据通用的`INSERT INTO SQL`语法：

``` sql
INSERT INTO table_name (field1, field2, ..., fieldN) VALUES (value1, value2, ..., valueN);
```

如果数据是字符型，必须使用`单引号`或者`双引号`。
&emsp;&emsp;我们将使用`SQL INSERT INTO`语句向`MySQL`数据表`w3cschool_tbl`插入数据：

``` sql
mysql> INSERT INTO w3cschool_tbl
     ->(w3cschool_title, w3cschool_author, submission_date)
     ->VALUES
     ->("Learn PHP", "John Poul", NOW());
Query OK, 1 row affected (0.01 sec)

mysql> INSERT INTO w3cschool_tbl
     ->(w3cschool_title, w3cschool_author, submission_date)
     ->VALUES
     ->("JAVA Tutorial", "Sanjay", '2007-05-06');
Query OK, 1 row affected (0.01 sec)
```

&emsp;&emsp;注意，箭头标记`->`不是`SQL`语句的一部分，它仅仅表示一个新行，如果一条`SQL`语句太长，我们可以通过回车键来创建一个新行来编写`SQL`语句。
&emsp;&emsp;在以上实例中，我们并没有提供`w3cschool_id`的数据，因为该字段我们在创建表的时候已经设置它为`AUTO_INCREMENT`(自动增加)属性。所以，该字段会自动递增而不需要我们去设置。
&emsp;&emsp;`NOW`是一个`MySQL`函数，该函数返回日期和时间。

### MySQL查询数据

&emsp;&emsp;以下为在`MySQL`数据库中查询数据通用的`SELECT`语法：

``` sql
SELECT column_name,column_name FROM table_name [WHERE Clause] [OFFSET M][LIMIT N]
```

- 在查询语句中，你可以使用一个或者多个表，表之间使用逗号`,`分割，并使用`WHERE`语句来设定查询条件。
- `SELECT`命令可以读取一条或者多条记录。
- 你可以使用星号`*`来代替其他字段，`SELECT`语句会返回表的所有字段数据。
- 你可以使用`WHERE`语句来包含任何条件。
- 你可以通过`OFFSET`指定`SELECT`语句开始查询的数据偏移量，默认情况下偏移量为`0`。
- 你可以使用`LIMIT`属性来设定返回的记录数。

&emsp;&emsp;以下实例将返回数据表`w3cschool_tbl`的所有记录：

``` sql
mysql> SELECT * from w3cschool_tbl;
+--------------+-----------------+------------------+-----------------+
| w3cschool_id | w3cschool_title | w3cschool_author | submission_date |
+--------------+-----------------+------------------+-----------------+
|            1 | Learn PHP       | John Poul        | 2017-04-05      |
|            2 | JAVA Tutorial   | Sanjay           | 2007-05-06      |
+--------------+-----------------+------------------+-----------------+
2 rows in set (0.00 sec)
```

### MySQL的where子句

&emsp;&emsp;以下是`SQL SELECT`语句使用`WHERE`子句从数据表中读取数据的通用语法：

``` sql
SELECT field1, field2, ..., fieldN FROM table_name1, table_name2... [WHERE condition1 [AND [OR]] condition2...
```

- 查询语句中你可以使用一个或者多个表，表之间使用逗号`,`分割，并使用`WHERE`语句来设定查询条件。
- 你可以在`WHERE`子句中指定任何条件。
- 你可以使用`AND`或者`OR`指定一个或多个条件。
- `WHERE`子句也可以运用于`SQL`的`DELETE`或者`UPDATE`命令。
- `WHERE`子句类似于程序语言中的`if`条件，根据`MySQL`表中的字段值来读取指定的数据。

&emsp;&emsp;以下为操作符列表，可用于`WHERE`子句中。下表中实例假定`A`为`10`，`B`为`20`：

操作符     | 描述                                         | 实例
-----------|---------------------------------------------|------
`=`        | 等号，检测两个值是否相等                      | `A = B`返回`false`
`<>`或`!=` | 不等于，检测两个值是否不相等                   | `A != B`返回`true`
`>`        | 大于号，检测左边的值是否大于右边的值            | `A > B`返回`false`
`<`        | 小于号，检测左边的值是否小于右边的值            | `A < B`返回`true`
`>=`       | 大于等于号，检测左边的值是否大于或等于右边的值   | `A >= B`返回`false`
`<=`       | 小于等于号，检测左边的值是否小于于或等于右边的值 | `A <= B`返回`true`

&emsp;&emsp;如果我们想在`MySQL`数据表中读取指定的数据，`WHERE`子句是非常有用的。使用主键来作为`WHERE`子句的条件查询是非常快速的。如果给定的条件在表中没有任何匹配的记录，那么查询不会返回任何数据。
&emsp;&emsp;以下实例将读取`w3cschool_tbl`表中`w3cschool_author`字段值为`Sanjay`的所有记录：

``` sql
mysql> SELECT * from w3cschool_tbl WHERE BINARY w3cschool_author='Sanjay';
+--------------+-----------------+------------------+-----------------+
| w3cschool_id | w3cschool_title | w3cschool_author | submission_date |
+--------------+-----------------+------------------+-----------------+
|            2 | JAVA Tutorial   | Sanjay           | 2007-05-06      |
+--------------+-----------------+------------------+-----------------+
1 row in set (0.00 sec)
```

&emsp;&emsp;除非你使用`LIKE`来比较字符串，否则`MySQL`的`WHERE`子句的字符串比较是不区分大小写的。你可以使用`BINARY`关键字来设定`WHERE`子句的字符串比较是区分大小写的。

### MySQL的UPDATE查询

&emsp;&emsp;如果我们需要修改或更新`MySQL`中的数据，可以使用`UPDATE`命令来操作：

``` sql
UPDATE table_name SET field1=new-value1, field2=new-value2 [WHERE Clause]
```

- 你可以同时更新一个或多个字段。
- 你可以在`WHERE`子句中指定任何条件。
- 你可以在一个单独表中同时更新数据。

&emsp;&emsp;当你需要更新数据表中指定行的数据时，`WHERE`子句是非常有用的。以下实例将更新数据表中`w3cschool_id`为`3`的`w3cschool_title`字段值：

``` sql
mysql> UPDATE w3cschool_tbl
    -> SET w3cschool_title='Learning JAVA'
    -> WHERE w3cschool_id=2;
Query OK, 1 row affected (0.04 sec)
Rows matched: 1  Changed: 1  Warnings: 0
```

### MySQL的DELETE语句

&emsp;&emsp;你可以使用`SQL`的`DELETE FROM`命令来删除`MySQL`数据表中的记录：

``` sql
DELETE FROM table_name [WHERE Clause]
```

- 如果没有指定`WHERE`子句，`MySQL`表中的所有记录将被删除。
- 你可以在`WHERE`子句中指定任何条件。
- 你可以在单个表中一次性删除记录。

&emsp;&emsp;当你想删除数据表中指定的记录时，`WHERE`子句是非常有用的。以下实例将删除`w3cschool_tbl`表中`w3cschool_id`为`2`的记录：

``` sql
mysql> DELETE FROM w3cschool_tbl WHERE w3cschool_id=2;
Query OK, 1 row affected (0.23 sec)
```

### MySQL的LIKE子句

&emsp;&emsp;我们知道在`MySQL`中使用`SELECT`命令来读取数据，同时可以在`SELECT`语句中使用`WHERE`子句来获取指定的记录。`WHERE`子句中可以使用等号`=`来设定获取数据的条件，如`w3cschool_author = 'Sanjay'`。
&emsp;&emsp;但是有时候我们需要获取`w3cschool_author`字段含有`jay`字符串的所有记录，这时就需要在`WHERE`子句中使用`LIKE`子句。
&emsp;&emsp;`LIKE`子句中使用百分号`%`字符来表示任意字符，类似于`UNIX`或正则表达式中的星号`*`。如果没有使用百分号`%`，`LIKE`子句与等号`=`的效果是一样的。
&emsp;&emsp;以下是`SELECT`语句使用`LIKE`子句从数据表中读取数据的通用语法：

``` sql
SELECT field1, field2, ..., fieldN table_name1, table_name2, ... WHERE field1 LIKE condition1 [AND [OR]] filed2 = 'somevalue'
```

- 你可以在`WHERE`子句中指定任何条件。
- 你可以在`WHERE`子句中使用`LIKE`子句。
- 你可以使用`LIKE`子句代替等号`=`。
- `LIKE`通常与`%`一同使用，类似于一个元字符的搜索。
- 你可以使用`AND`或者`OR`指定一个或多个条件。
- 你可以在`DELETE`或`UPDATE`命令中使用`WHERE...LIKE`子句来指定条件。

&emsp;&emsp;以下是从`w3cschool_tbl`表中获取`w3cschool_author`字段中含有字符`n`的所有记录：

``` sql
mysql> SELECT * from w3cschool_tbl WHERE w3cschool_author LIKE '%n%';
+--------------+-----------------+------------------+-----------------+
| w3cschool_id | w3cschool_title | w3cschool_author | submission_date |
+--------------+-----------------+------------------+-----------------+
|            1 | Learn PHP       | John Poul        | 2017-04-05      |
|            3 | JAVA Tutorial   | Sanjay           | 2007-05-06      |
+--------------+-----------------+------------------+-----------------+
2 rows in set (0.00 sec)
```

### MySQL排序

&emsp;&emsp;如果我们需要对读取的数据进行排序，可以使用`ORDER BY`子句来设定你想按哪个字段哪种方式来进行排序，再返回搜索结果。
&emsp;&emsp;以下是`SELECT`语句使用`ORDER BY`子句将查询数据排序后再返回数据：

``` sql
SELECT field1, field2, ..., fieldN table_name1, table_name2, ... ORDER BY field1, [field2...] [ASC [DESC]]
```

- 你可以使用任何字段来作为排序的条件，从而返回排序后的查询结果。
- 你可以设定多个字段来排序。
- 你可以使用`ASC`或`DESC`关键字来设置查询结果是按升序或降序排列。默认情况下，它是按升排列。
- 你可以添加`WHERE...LIKE`子句来设置条件。

&emsp;&emsp;读取`w3cschool_tbl`表中所有数据，并按`w3cschool_author`字段的升序或降序排列：

``` sql
mysql> SELECT * from w3cschool_tbl ORDER BY w3cschool_author ASC;
+--------------+-----------------+------------------+-----------------+
| w3cschool_id | w3cschool_title | w3cschool_author | submission_date |
+--------------+-----------------+------------------+-----------------+
|            2 | Learn MySQL     | Abdul S          | 2007-05-24      |
|            1 | Learn PHP       | John Poul        | 2007-05-24      |
|            3 | JAVA Tutorial   | Sanjay           | 2007-05-06      |
+--------------+-----------------+------------------+-----------------+
3 rows in set (0.00 sec)

mysql> SELECT * from w3cschool_tbl ORDER BY w3cschool_author DESC;
+--------------+-----------------+------------------+-----------------+
| w3cschool_id | w3cschool_title | w3cschool_author | submission_date |
+--------------+-----------------+------------------+-----------------+
|            3 | JAVA Tutorial   | Sanjay           | 2007-05-06      |
|            1 | Learn PHP       | John Poul        | 2007-05-24      |
|            2 | Learn MySQL     | Abdul S          | 2007-05-24      |
+--------------+-----------------+------------------+-----------------+
3 rows in set (0.00 sec)
```

### MySQL分组

&emsp;&emsp;`MySQL`的`GROUP BY`语句根据一个或多个列对结果集进行分组。在分组的列上，我们可以使用`COUNT`、`SUM`、`AVG`等函数。`GROUP BY`语法如下：

``` sql
SELECT column_name, function(column_name) FROM table_name WHERE column_name operator value GROUP BY column_name;
```

&emsp;&emsp;本章节实例使用到了以下表结构及数据。在使用前，我们可以先将以下数据导入数据库中：

``` sql
SET NAMES utf8;
SET FOREIGN_KEY_CHECKS = 0;

-- ----------------------------
--  Table structure for `employee_tbl`
-- ----------------------------
DROP TABLE IF EXISTS `employee_tbl`;
CREATE TABLE `employee_tbl` (
    `id` int(11) NOT NULL,
    `name` char(10) NOT NULL DEFAULT '',
    `date` datetime NOT NULL,
    `singin` tinyint(4) NOT NULL DEFAULT '0' COMMENT '登录次数',
    PRIMARY KEY (`id`)
) ENGINE=InnoDB DEFAULT CHARSET=utf8;

-- ----------------------------
--  Records of `employee_tbl`
-- ----------------------------
BEGIN;
INSERT INTO `employee_tbl` VALUES
    ('1', '小明', '2016-04-22 15:25:33', '1'),
    ('2', '小王', '2016-04-20 15:25:47', '3'),
    ('3', '小丽', '2016-04-19 15:26:02', '2'),
    ('4', '小王', '2016-04-07 15:26:14', '4'),
    ('5', '小明', '2016-04-11 15:26:40', '4'),
    ('6', '小明', '2016-04-04 15:26:54', '2');
COMMIT;

SET FOREIGN_KEY_CHECKS = 1;
```

导入成功后，执行以下`SQL`语句：

``` sql
mysql> SELECT * FROM employee_tbl;
+----+--------+---------------------+--------+
| id | name   | date                | singin |
+----+--------+---------------------+--------+
|  1 | 小明   | 2016-04-22 15:25:33 |      1 |
|  2 | 小王   | 2016-04-20 15:25:47 |      3 |
|  3 | 小丽   | 2016-04-19 15:26:02 |      2 |
|  4 | 小王   | 2016-04-07 15:26:14 |      4 |
|  5 | 小明   | 2016-04-11 15:26:40 |      4 |
|  6 | 小明   | 2016-04-04 15:26:54 |      2 |
+----+--------+---------------------+--------+
6 rows in set (0.00 sec)
```

接下来我们使用`GROUP BY`语句将数据表按名字进行分组，并统计每个人有多少条记录：

``` sql
mysql> SELECT name, COUNT(*) FROM employee_tbl GROUP BY name;
+--------+----------+
| name   | COUNT(*) |
+--------+----------+
| 小丽   |        1 |
| 小明   |        3 |
| 小王   |        2 |
+--------+----------+
3 rows in set (0.01 sec)
```

&emsp;&emsp;`WITH ROLLUP`可以实现在分组统计数据基础上再进行数据的统计(`SUM`、`AVG`、`COUNT`、`...`)。例如我们将以上的数据表按名字进行分组，再统计每个人登录的次数：

``` sql
mysql> SELECT name, SUM(singin) as singin_count FROM employee_tbl GROUP BY name WITH ROLLUP;
+--------+--------------+
| name   | singin_count |
+--------+--------------+
| 小丽   |            2 |
| 小明   |            7 |
| 小王   |            7 |
| NULL   |           16 |
+--------+--------------+
4 rows in set (0.00 sec)
```

其中记录`NULL`表示所有人的登录次数。
&emsp;&emsp;我们可以使用`coalesce`来设置一个可以取代`NUll`的名称。`coalesce`的语法如下：

``` sql
select coalesce(a, b, c);
```

- 如果`a == null`，则选择`b`。
- 如果`b == null`，则选择`c`。
- 如果`a != null`，则选择`a`。
- 如果`a`、`b`和`c`都为`null`，则返回为`null`(没意义)。

&emsp;&emsp;以下实例中，如果名字为`NULL`，我们使用总数代替：

``` sql
mysql> SELECT coalesce(name, '总数'), SUM(singin) as singin_count FROM  employee_tbl GROUP BY name WITH ROLLUP;
+------------------------+--------------+
| coalesce(name, '总数') | singin_count |
+------------------------+--------------+
| 小丽                   |            2 |
| 小明                   |            7 |
| 小王                   |            7 |
| 总数                   |           16 |
+------------------------+--------------+
4 rows in set (0.01 sec)
```

### MySQL连接的使用

&emsp;&emsp;我们已经学会了如何在一张表中读取数据，这是相对简单的。但是在真正的应用中，经常需要从多个数据表中读取数据。
&emsp;&emsp;本章节将向大家介绍如何使用`JOIN`在两个或多个表中查询数据。你可以在`SELECT`、`UPDATE`和`DELETE`语句中使用`JOIN`来联合多表查询。
&emsp;&emsp;`JOIN`按照功能大致分为如下`3`类：

- `INNER JOIN`(内连接或等值连接)：获取两个表中字段匹配关系的记录。
- `LEFT JOIN`(左连接)：获取左表所有记录，即使右表没有对应匹配的记录。
- `RIGHT JOIN`(右连接)：与`LEFT JOIN`相反，用于获取右表所有记录，即使左表没有对应匹配的记录。

&emsp;&emsp;在`W3CSCHOOL`数据库中有两张表`tcount_tbl`和`w3cschool_tbl`，数据分别如下：

``` sql
mysql> SELECT * FROM tcount_tbl;
+------------------+-----------------+
| w3cschool_author | w3cschool_count |
+------------------+-----------------+
| mahran           |              20 |
| mahnaz           |            NULL |
| Jen              |            NULL |
| Gill             |              20 |
| John Poul        |               1 |
| Sanjay           |               1 |
+------------------+-----------------+
6 rows in set (0.01 sec)

mysql> SELECT * from w3cschool_tbl;
+--------------+-----------------+------------------+-----------------+
| w3cschool_id | w3cschool_title | w3cschool_author | submission_date |
+--------------+-----------------+------------------+-----------------+
|            1 | Learn PHP       | John Poul        | 2007-05-24      |
|            2 | Learn MySQL     | Abdul S          | 2007-05-24      |
|            3 | JAVA Tutorial   | Sanjay           | 2007-05-06      |
+--------------+-----------------+------------------+-----------------+
3 rows in set (0.00 sec)
```

&emsp;&emsp;接下来就使用`INNER JOIN`(也可以省略`INNER`，只使用`JOIN`，效果一样)来连接以上两张表，以读取`w3cschool_tbl`表中所有`w3cschool_author`字段在`tcount_tbl`表对应的`w3cschool_count`字段值：

``` sql
mysql> SELECT a.w3cschool_id, a.w3cschool_author, b.w3cschool_count FROM w3cschool_tbl a INNER JOIN tcount_tbl b ON a.w3cschool_author = b.w3cschool_author;
+--------------+------------------+-----------------+
| w3cschool_id | w3cschool_author | w3cschool_count |
+--------------+------------------+-----------------+
|            1 | John Poul        |               1 |
|            3 | Sanjay           |               1 |
+--------------+------------------+-----------------+
2 rows in set (0.00 sec)
```

&emsp;&emsp;以上`SQL`语句等价于：

``` sql
mysql> SELECT a.w3cschool_id, a.w3cschool_author, b.w3cschool_count FROM w3cschool_tbl a, tcount_tbl b WHERE a.w3cschool_author = b.w3cschool_author;
+--------------+------------------+-----------------+
| w3cschool_id | w3cschool_author | w3cschool_count |
+--------------+------------------+-----------------+
|           1  | John Poul        |               1 |
|           3  | Sanjay           |               1 |
+--------------+------------------+-----------------+
2 rows in set (0.01 sec)
```

&emsp;&emsp;`MySQL`的`left join`与`join`有所不同。`LEFT JOIN`会读取左边数据表的全部数据，即便右边表无对应数据。
&emsp;&emsp;尝试以下实例，以`w3cschool_tbl`为左表，`tcount_tbl`为右表，理解`LEFT JOIN`的应用：

``` sql
mysql> SELECT a.w3cschool_id, a.w3cschool_author, b.w3cschool_count FROM w3cschool_tbl a LEFT JOIN tcount_tbl b ON a.w3cschool_author = b.w3cschool_author;
+--------------+------------------+-----------------+
| w3cschool_id | w3cschool_author | w3cschool_count |
+--------------+------------------+-----------------+
|            1 | John Poul        |               1 |
|            2 | Abdul S          |            NULL |
|            3 | Sanjay           |               1 |
+--------------+------------------+-----------------+
3 rows in set (0.02 sec)
```

该语句会读取左边的数据表`w3cschool_tbl`所有选取的字段数据，即便在右侧表`tcount_tbl`中没有对应的`w3cschool_author`字段值。
&emsp;&emsp;`MySQL`的`RIGHT JOIN`会读取右边数据表的全部数据，即便左侧表无对应数据。
&emsp;&emsp;尝试以下实例，以`tcount_tbl`为左表，`w3cschool_tbl`为右表，理解`RIGHT JOIN`的应用：

``` sql
mysql> SELECT b.w3cschool_id, b.w3cschool_author, a.w3cschool_count FROM tcount_tbl a RIGHT JOIN w3cschool_tbl b ON a.w3cschool_author = b.w3cschool_author;
+--------------+------------------+-----------------+
| w3cschool_id | w3cschool_author | w3cschool_count |
+--------------+------------------+-----------------+
|            1 | John Poul        |               1 |
|            2 | Abdul S          |            NULL |
|            3 | Sanjay           |               1 |
+--------------+------------------+-----------------+
3 rows in set (0.02 sec)
```

以上实例中使用了`RIGHT JOIN`，该语句会读取右边的数据表`w3cschool_tbl`所有选取的字段数据，即便在左侧表`tcount_tbl`中没有对应的`w3cschool_author`字段值。

### MySQL的NULL值处理

&emsp;&emsp;`MySQL`使用`SELECT`命令及`WHERE`子句来读取数据表中的数据，但是当提供的查询条件字段为`NULL`时，该命令可能就无法正常工作。
&emsp;&emsp;为了处理这种情况，`MySQL`提供了`3`大运算符：

- `IS NULL`：当列的值是`NULL`，此运算符返回`true`。
- `IS NOT NULL`：当列的值不为`NULL`，运算符返回`true`。
- `<=>`：比较操作符(不同于`=`运算符)，当比较的两个值为`NULL`，时返回`true`。

&emsp;&emsp;`NULL`的条件比较运算是比较特殊的，你不能使用`= NULL`或`!= NULL`在列中查找`NULL`值。在`MySQL`中，`NULL`值与任何其它值的比较(即使是`NULL`)永远返回`false`。`MySQL`中处理`NULL`使用`IS NULL`和`IS NOT NULL`运算符。
&emsp;&emsp;以下实例中假设数据库`W3CSCHOOL`中的表`tcount_tbl`含有两列`w3cschool_author`和`w3cschool_count`，在`w3cschool_count`中设置插入`NULL`值：

``` sql
mysql> create table tcount_tbl
    -> (
    -> w3cschool_author varchar(40) NOT NULL,
    -> w3cschool_count  INT
    -> );
Query OK, 0 rows affected (0.05 sec)

mysql> INSERT INTO tcount_tbl
    -> (w3cschool_author, w3cschool_count) values ('mahran', 20);

mysql> INSERT INTO tcount_tbl
    -> (w3cschool_author, w3cschool_count) values ('mahnaz', NULL);

mysql> INSERT INTO tcount_tbl
    -> (w3cschool_author, w3cschool_count) values ('Jen', NULL);

mysql> INSERT INTO tcount_tbl
    -> (w3cschool_author, w3cschool_count) values ('Gill', 20);

mysql> SELECT * from tcount_tbl;
+------------------+-----------------+
| w3cschool_author | w3cschool_count |
+------------------+-----------------+
| mahran           |              20 |
| mahnaz           |            NULL |
| Jen              |            NULL |
| Gill             |              20 |
+------------------+-----------------+
4 rows in set (0.00 sec)
```

&emsp;&emsp;以下实例中，你可以看到`=`和`!=`运算符是不起作用的：

``` sql
mysql> SELECT * FROM tcount_tbl WHERE w3cschool_count = NULL;
Empty set (0.00 sec)
mysql> SELECT * FROM tcount_tbl WHERE w3cschool_count != NULL;
Empty set (0.01 sec)
```

&emsp;&emsp;查找数据表中`w3cschool_count`为`NULL`的数据，必须使用`IS NULL`和`IS NOT NULL`：

``` sql
mysql> SELECT * FROM tcount_tbl
    -> WHERE w3cschool_count IS NULL;
+------------------+-----------------+
| w3cschool_author | w3cschool_count |
+------------------+-----------------+
| mahnaz           |            NULL |
| Jen              |            NULL |
+------------------+-----------------+
2 rows in set (0.00 sec)

mysql> SELECT * from tcount_tbl
    -> WHERE w3cschool_count IS NOT NULL;
+------------------+-----------------+
| w3cschool_author | w3cschool_count |
+------------------+-----------------+
| mahran           |              20 |
| Gill             |              20 |
+------------------+-----------------+
2 rows in set (0.00 sec)
```

### MySQL正则表达式

&emsp;&emsp;在前面的章节，我们已经了解到`MySQL`可以通过`LIKE...%`来进行模糊匹配。`MySQL`同样也支持其他正则表达式的匹配，使用`REGEXP`操作符来进行正则表达式匹配。
&emsp;&emsp;下表中的正则模式可应用于`REGEXP`操作符中：

模式                            | 描述
--------------------------------|-----
`^`                             | 匹配输入字符串的开始位置。如果设置了`RegExp`对象的`Multiline`属性，`^`也匹配`\n`或`\r`之后的位置
`$`                             | 匹配输入字符串的结束位置。如果设置了`RegExp`对象的`Multiline`属性，`$`也匹配`\n`或`\r`之前的位置
`.`                             | 匹配除`\n`之外的任何单个字符。要匹配包括`\n`在内的任何字符，请使用像`[.\n]`的模式
`[...]`                         | 字符集合，匹配所包含的任意一个字符。例如`[abc]`可以匹配`plain`中的`a`
`[^...]`                        | 负值字符集合，匹配未包含的任意字符。例如`[^abc]`可以匹配`plain`中的`p`
<code>p1&#124;p2&#124;p3</code> | 匹配`p1`或`p2`或`p3`。例如，<code>z&#124;food</code>能匹配`z`或`food`。<code>(z&#124;f)ood</code>则匹配`zood`或`food`
`*`                             | 匹配前面的子表达式`零次`或`多次`。例如，`zo*`能匹配`z`以及`zoo`。`*`等价于`{0,}`
`+`                             | 匹配前面的子表达式`一次`或`多次`。例如，`zo+`能匹配`zo`以及`zoo`，但不能匹配`z`。`+`等价于`{1,}`
`{n}`                           | `n`是一个非负整数，匹配确定的`n`次。例如，`o{2}`不能匹配`Bob`中的`o`，但是能匹配`food`中的两个`o`
`{n,m}`                         | `m`和`n`均为非负整数，其中`n <= m`。最少匹配`n`次且最多匹配`m`次

&emsp;&emsp;了解以上的正则需求后，我们就可以根据自己的需求来编写带有正则表达式的`SQL`语句：

- `SELECT name FROM person_tbl WHERE name REGEXP '^st';`：查找`name`字段中以`st`为开头的所有数据。
- `SELECT name FROM person_tbl WHERE name REGEXP 'ok$';`：查找`name`字段中以`ok`为结尾的所有数据。
- `SELECT name FROM person_tbl WHERE name REGEXP 'mar';`：查找`name`字段中包含`mar`字符串的所有数据。
- `SELECT name FROM person_tbl WHERE name REGEXP '^[aeiou]|ok$';`：查找`name`字段中以元音字符开头且以`ok`字符串结尾的所有数据。

### MySQL的ALTER命令

&emsp;&emsp;当我们需要修改数据表名或者修改数据表字段时，就需要使用到`ALTER`命令：

``` sql
mysql> create table testalter_tbl
    -> (
    -> i INT,
    -> c CHAR(1)
    -> );
Query OK, 0 rows affected (0.05 sec)

mysql> SHOW COLUMNS FROM testalter_tbl;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| i     | int(11) | YES  |     | NULL    |       |
| c     | char(1) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

#### 删除、添加或修改表字段

&emsp;&emsp;如下命令使用了`ALTER`命令及`DROP`子句来删除以上创建表的`i`字段：

``` sql
mysql> ALTER TABLE testalter_tbl DROP i;
```

如果数据表中只剩余一个字段，则无法使用`DROP`来删除字段。
&emsp;&emsp;`MySQL`中使用`ADD`子句来向数据表中添加列。如下实例在表`testalter_tbl`中添加`i`字段，并定义数据类型：

``` sql
mysql> ALTER TABLE testalter_tbl ADD i INT;
```

执行以上命令后，`i`字段会自动添加到数据表字段的末尾。

``` sql
mysql> SHOW COLUMNS FROM testalter_tbl;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| c     | char(1) | YES  |     | NULL    |       |
| i     | int(11) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

&emsp;&emsp;如果你需要指定新增字段的位置，可以使用`MySQL`提供的关键字`FIRST`(设定为第一列)、`AFTER 字段名`(设定位于某个字段之后)。尝试以下语句，在执行成功后，使用`SHOW COLUMNS`查看表结构的变化：

``` sql
ALTER TABLE testalter_tbl DROP i;
ALTER TABLE testalter_tbl ADD i INT FIRST;
ALTER TABLE testalter_tbl DROP i;
ALTER TABLE testalter_tbl ADD i INT AFTER c;
```

&emsp;&emsp;`FIRST`和`AFTER`关键字只占用于`ADD`子句，所以如果你想重置数据表字段的位置，就需要先使用`DROP`删除字段，然后使用`ADD`来添加字段并设置位置。

#### 修改字段类型及名称

&emsp;&emsp;如果需要修改字段类型及名称，你可以在`ALTER`命令中使用`MODIFY`或`CHANGE`子句。例如，把字段`c`的类型从`CHAR(1)`改为`CHAR(10)`，可以执行以下命令：

``` sql
mysql> ALTER TABLE testalter_tbl MODIFY c CHAR(10);
```

&emsp;&emsp;如果使用`CHANGE`子句，语法有很大的不同。在`CHANGE`关键字之后，紧跟着的是你要修改的字段名，然后指定新字段的类型及名称：

``` sql
mysql> ALTER TABLE testalter_tbl CHANGE i j BIGINT;
```

&emsp;&emsp;如果你现在想把字段`j`从`BIGINT`修改为`INT`，`SQL`语句如下：

``` sql
mysql> ALTER TABLE testalter_tbl CHANGE j j INT;
```

#### ALTER TABLE对Null值和默认值的影响

&emsp;&emsp;当你修改字段时，你可以指定是否包含值，或者是否设置默认值。以下实例指定字段`j`为`NOT NULL`，且默认值为`100`：

``` sql
mysql> ALTER TABLE testalter_tbl MODIFY j BIGINT NOT NULL DEFAULT 100;
```

如果你不设置默认值，`MySQL`会自动设置该字段默认为`NULL`。

#### 修改字段默认值

&emsp;&emsp;你可以使用`ALTER`来修改字段的默认值：

``` sql
mysql> ALTER TABLE testalter_tbl ALTER i SET DEFAULT 1000;
mysql> SHOW COLUMNS FROM testalter_tbl;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| c     | char(1) | YES  |     | NULL    |       |
| i     | int(11) | YES  |     | 1000    |       |
+-------+---------+------+-----+---------+-------+
2 rows in set (0.00 sec)
```

&emsp;&emsp;你也可以使用`ALTER`命令及`DROP`子句来删除字段的默认值：

``` sql
mysql> ALTER TABLE testalter_tbl ALTER i DROP DEFAULT;
mysql> SHOW COLUMNS FROM testalter_tbl;
+-------+---------+------+-----+---------+-------+
| Field | Type    | Null | Key | Default | Extra |
+-------+---------+------+-----+---------+-------+
| c     | char(1) | YES  |     | NULL    |       |
| i     | int(11) | YES  |     | NULL    |       |
+-------+---------+------+-----+---------+-------+
2 rows in set (0.00 sec)
Changing a Table Type:
```

#### 修改表名

&emsp;&emsp;如果需要修改数据表的名称，可以在`ALTER TABLE`语句中使用`RENAME`子句来实现。以下实例将数据表`testalter_tbl`重命名为`alter_tbl`：

``` sql
mysql> ALTER TABLE testalter_tbl RENAME TO alter_tbl;
```