---
title: sqlite3模块
categories: Python语法
date: 2019-01-12 10:15:33
---
### 连接数据库

&emsp;&emsp;下面的代码显示如何连接到一个现有的数据库。如果该数据库不存在，那么它将会被创建：<!--more-->

``` python
import sqlite3
conn = sqlite3.connect('test.db')
```

也可以把数据库名称赋值为`:memory:`，这样就会在`RAM`中创建一个数据库。

### 创建表

&emsp;&emsp;在数据库中创建一个数据表：

``` python
import sqlite3

conn = sqlite3.connect('test.db')
c = conn.cursor()
c.execute('''CREATE TABLE COMPANY
       (ID INT PRIMARY KEY     NOT NULL,
        NAME           TEXT    NOT NULL,
        AGE            INT     NOT NULL,
        ADDRESS        CHAR(50),
        SALARY         REAL);''')
conn.commit()
conn.close()
```

### INSERT操作

&emsp;&emsp;在表`COMPANY`中创建记录：

``` python
import sqlite3

conn = sqlite3.connect('test.db')
c = conn.cursor()
c.execute("INSERT INTO COMPANY (ID, NAME, AGE, ADDRESS, SALARY) \
           VALUES (1, 'Paul', 32, 'California', 20000.00)")
c.execute("INSERT INTO COMPANY (ID, NAME, AGE, ADDRESS, SALARY) \
           VALUES (2, 'Allen', 25, 'Texas', 15000.00)")
conn.commit()
conn.close()
```

### SELECT操作

&emsp;&emsp;从表`COMPANY`中获取并显示记录：

``` python
import sqlite3

conn = sqlite3.connect('test.db')
c = conn.cursor()
cursor = c.execute("SELECT id, name, address, salary from COMPANY")

for row in cursor:
    print("ID =", row[0])
    print("NAME =", row[1])
    print("ADDRESS =", row[2])
    print("SALARY =", row[3])
    print("----------")

conn.close()
```

执行结果：

``` python
ID = 1
NAME = Paul
ADDRESS = California
SALARY = 20000.0
----------
ID = 2
NAME = Allen
ADDRESS = Texas
SALARY = 15000.0
----------
```

### UPDATE操作

&emsp;&emsp;使用`UPDATE`语句来更新记录：

``` python
import sqlite3

conn = sqlite3.connect('test.db')
c = conn.cursor()
c.execute("UPDATE COMPANY set SALARY = 25000.00 where ID=1")
conn.commit()
conn.close()
```

### DELETE操作

&emsp;&emsp;使用`DELETE`语句删除记录：

``` python
import sqlite3

conn = sqlite3.connect('test.db')
c = conn.cursor()
c.execute("DELETE from COMPANY where ID = 2;")
conn.commit()
conn.close()
```