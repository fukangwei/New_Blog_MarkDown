---
title: sqlite教程
date: 2021-07-20 06:09:03
categories: 数据库
---
### 创建数据库

&emsp;&emsp;`sqlite3`命令被用来创建新的`SQLite`：<!--more-->

``` bash
sqlite3 testDB.db
```

上面的命令将在当前目录下创建文件`testDB.db`。
&emsp;&emsp;`sqlite3`命令在成功创建数据库文件后，将提供一个`sqlite>`提示符，可以使用`.quit`退出提示符。

### 数据类型

&emsp;&emsp;`sqlite`可以使用如下数据类型：

数据类型   | 描述            | 数据类型   | 描述
----------|-----------------|-----------|-----
`NULL`    | 值是一个`NULL`值 | `INTEGER` | 值是一个带符号的整数
`REAL`    | 值是一个浮点值   | `TEXT`    | 值是一个文本字符串

### 创建表

&emsp;&emsp;创建一个名为`COMPANY`的数据表，`ID`作为主键，`NOT NULL`表示在表中创建纪录时，这些字段不能为`NULL`：

``` sql
CREATE TABLE COMPANY(
   ID INT PRIMARY KEY     NOT NULL,
   NAME           TEXT    NOT NULL,
   AGE            INT     NOT NULL,
   ADDRESS        CHAR(50),
   SALARY         REAL
);
```

### 删除表

&emsp;&emsp;`DROP TABLE`用来删除数据表：

``` sql
DROP TABLE COMPANY;
```

### 插入数据

&emsp;&emsp;`INSERT`命令用于向数据表中插入数据：

``` sql
INSERT INTO COMPANY (ID, NAME, AGE, ADDRESS, SALARY) VALUES (1, 'Paul', 32, 'California', 20000.00);
INSERT INTO COMPANY (ID, NAME, AGE, ADDRESS, SALARY) VALUES (2, 'Allen', 25, 'Texas', 15000.00);
```

&emsp;&emsp;另一种语法如下：

``` sql
INSERT INTO COMPANY VALUES (7, 'James', 24, 'Houston', 10000.00);
```

### 查询数据表

&emsp;&emsp;使用`SELECT`获取并显示所有记录：

``` sql
SELECT * FROM COMPANY;
```

&emsp;&emsp;获取`COMPANY`表中指定的字段：

``` sql
SELECT ID, NAME, SALARY FROM COMPANY;
```

### WHERE子句

&emsp;&emsp;`WHERE`子句用于指定从数据表中获取数据的条件。
&emsp;&emsp;列出`AGE`大于等于`25`，且工资大于等于`15000`的所有记录：

``` sql
SELECT * FROM COMPANY WHERE AGE >= 25 AND SALARY >= 15000;
```

### UPDATE语句

&emsp;&emsp;`UPDATE`语句用于修改数据表中已有的记录。
&emsp;&emsp;更新`ID`为`6`的客户地址为`Texas`：

``` sql
UPDATE COMPANY SET ADDRESS = 'Texas' WHERE ID = 7;
```

### DELETE语句

&emsp;&emsp;`DELETE`语句用于删除数据表中已有的记录。
&emsp;&emsp;删除`ID`为`7`的客户：

``` sql
DELETE FROM COMPANY WHERE ID = 7;
```