---
title: Qt之sqlite数据库
categories: Qt语法详解
date: 2019-02-02 12:45:15
---
&emsp;&emsp;在使用数据库功能时，需要在`.pro`文件中增加语句`QT += sql`，同时在代码中增加头文件`QtSql`。<!--more-->
&emsp;&emsp;`QtSql`模块提供了与平台以及数据库种类无关的访问`SQL`数据库的接口，这个接口由利用`Qt`的模型视图结构将数据库与用户界面集成的一套类来支持。`QSqlDatabase`对象象征了数据库的关联。`Qt`使用驱动程序与各种数据库的应用编程接口进行通信。`Qt`包括如下驱动程序：

驱动程序    | 数据库
-----------|----------
`QDB2`     | `IBM DB2`
`QIBASE`   | `Borland InterBase`
`QMYSQL`   | `MySql`
`QOCI`     | 甲骨文公司(`Oracle Call Interface`)
`QODBC`    | `ODBC`(包括微软公司的`QSL`服务)
`QPSQL`    | `PostgreSQL`
`QSQLITE`  | `QSLite`第`3`版
`QSQLITE2` | `QSLite`第`2`版
`QTDS`     | `Qybase`自适应服务器

&emsp;&emsp;由于授权的许可限制，`Qt`的开源版本无法提供所有的驱动程序。当配置`Qt`时，既可以选择`Qt`本身包含的`SQL`驱动程序，也可以以插件的形式建立驱动程序。如下是`Qt`使用`SQLite`的基本操作：

``` cpp
/* 添加数据库驱动、设置数据库名称、数据库登录用户名、密码 */
QSqlDatabase database = QSqlDatabase::addDatabase ( "QSQLITE" );
database.setDatabaseName ( "database.db" );
database.setUserName ( "root" );
database.setPassword ( "123456" );

if ( !database.open() ) { /* 打开数据库 */
    qDebug() << database.lastError();
    qFatal ( "failed to connect." ) ;
} else {
    /* QSqlQuery类提供执行和操作的SQL语句的方法。可以用来执行DML(数据操作语言)语句，
       如SELECT、INSERT、UPDATE、DELETE，以及DDL(数据定义语言)语句，
       例如“CREATE TABLE”。也可以用来执行那些不是标准的SQL的数据库特定的命令 */
    QSqlQuery sql_query;
    QString create_sql = "create table student (id int primary key, name varchar(30), age int)";
    QString select_max_sql = "select max(id) from student";
    QString insert_sql = "insert into student values (?, ?, ?)";
    QString update_sql = "update student set name = :name where id = :id";
    QString select_sql = "select id, name from student";
    QString select_all_sql = "select * from student";
    QString delete_sql = "delete from student where id = ?";
    QString clear_sql = "delete from student";
    sql_query.prepare ( create_sql );

    if ( !sql_query.exec() ) {
        qDebug() << sql_query.lastError();
    } else {
        qDebug() << "table created!";
    }

    /* 查询最大id */
    int max_id = 0;
    sql_query.prepare ( select_max_sql );

    if ( !sql_query.exec() ) {
        qDebug() << sql_query.lastError();
    } else {
        while ( sql_query.next() ) {
            max_id = sql_query.value ( 0 ).toInt();
            qDebug() << QString ( "max id:%1" ).arg ( max_id );
        }
    }

    /* 插入数据 */
    sql_query.prepare ( insert_sql );
    sql_query.addBindValue ( max_id + 1 );
    sql_query.addBindValue ( "name" );
    sql_query.addBindValue ( 25 );

    if ( !sql_query.exec() ) {
        qDebug() << sql_query.lastError();
    } else {
        qDebug() << "inserted!";
    }

    /* 更新数据 */
    sql_query.prepare ( update_sql );
    sql_query.bindValue ( ":name", "Qt" );
    sql_query.bindValue ( ":id", 1 );

    if ( !sql_query.exec() ) {
        qDebug() << sql_query.lastError();
    } else {
        qDebug() << "updated!";
    }

    /* 查询部分数据 */
    if ( !sql_query.exec ( select_sql ) ) {
        qDebug() << sql_query.lastError();
    } else {
        while ( sql_query.next() ) {
            int id = sql_query.value ( "id" ).toInt();
            QString name = sql_query.value ( "name" ).toString();
            qDebug() << QString ( "id:%1    name:%2" ).arg ( id ).arg ( name );
        }
    }

    /* 查询所有数据 */
    sql_query.prepare ( select_all_sql );

    if ( !sql_query.exec() ) {
        qDebug() << sql_query.lastError();
    } else {
        while ( sql_query.next() ) {
            int id = sql_query.value ( 0 ).toInt();
            QString name = sql_query.value ( 1 ).toString();
            int age = sql_query.value ( 2 ).toInt();
            qDebug() << QString ( "id:%1 name:%2 age:%3" ).arg ( id ).arg ( name ).arg ( age );
        }
    }

    /* 删除数据 */
    sql_query.prepare ( delete_sql );
    sql_query.addBindValue ( max_id );

    if ( !sql_query.exec() ) {
        qDebug() << sql_query.lastError();
    } else {
        qDebug() << "deleted!";
    }

    /* 清空表 */
    sql_query.prepare ( clear_sql );

    if ( !sql_query.exec() ) {
        qDebug() << sql_query.lastError();
    } else {
        qDebug() << "cleared";
    }
}

database.close(); /* 关闭数据库 */
QFile::remove ( "database.db" ); /* 删除数据库 */
```

---

&emsp;&emsp;`QSqlDatabase`类实现了数据库连接的操作，`QSqlQuery`类执行`SQL`语句，`QSqlRecord`类封装数据库所有记录。

### QSqlDatabase类

&emsp;&emsp;使用示例如下：

``` cpp
QSqlDatabase db = QSqlDatabase::addDatabase ( "QOCI" );
db.setHostName ( "localhost" ); /* 数据库主机名 */
db.setDatabaseName ( "scott" ); /* 数据库名 */
db.setUserName ( "stott" ); /* 数据库用户名 */
db.setPassword ( "tiger" ); /* 数据库密码 */
db.open(); /* 打开数据库连接 */
db.close(); /* 释放数据库连接 */
```

&emsp;&emsp;建立数据库文件：

``` cpp
QSqlDatabase db = QSqlDatabase::addDatabase ( "QSQLITE" );
db.setDatabaseName ( "database.db" );

if ( !db.open() ) {
    qDebug ( "数据库不能打开" );
    return false;
}
```

&emsp;&emsp;建立数据库文件后，创建表并插入两条数据：

``` cpp
QSqlQuery query;
/* id自动增加 */
query.exec ( "create table student(id INTEGER PRIMARY KEY autoincrement, name nvarchar(20), age int)" );
query.exec ( "insert into student values(1, '小明', 14)" );
query.exec ( "insert into student values(2, '小王', 15)" );
```

### QSqlQuery类

&emsp;&emsp;插入值到数据库操作有两种方法：

- 直接用`SQL`语句插入(参照上面)。
- 利用预处理方式插入(`ORACLE`语法和`ODBC`语法)。适合插入多条记录，或者避免将值转换成字符串(即正确地转义)，调用`prepare`函数指定一个包含占位符的`query`，然后绑定要插入的值。

&emsp;&emsp;`ORACLE`语法如下：

``` cpp
QSqlQuery query;
/* 准备执行SQL查询 */
query.prepare ( "INSERT INTO T_STUDENT (name, age) VALUES (:name, :age)" );
query.bindValue ( ":name", "小王" ); /* 在绑定要插入的值 */
query.bindValue ( ":age", 11 );
query.exec();
```

&emsp;&emsp;`ODBC`语法如下：

``` cpp
QSqlQuery query;
/* 准备执行SQL查询 */
query.prepare ( "INSERT INTO T_STUDENT (name,age) VALUES (?,?)" );
query.addBindValue ( "小王" ); /* 在绑定要插入的值 */
query.addBindValue ( 11 );
query.exec();
```

&emsp;&emsp;批量插入到数据库中：

``` cpp
QSqlQuery query;
query.prepare ( “insert into student values ( ?, ? ) ” );
QVariantList names;
/* 如果要提交空串，用QVariant(QVariant::String)代替名字 */
names << "小王" << "小明" << "小张" << "小新";
query.addBindValue ( names );
QVariantList ages;
ages << 11 << 13 << 12 << 11;
query.addBindValue ( ages );

if ( !query.execBatch() ) { /* 进行批处理，如果出错就输出错误 */
    qDebug() << query.lastError();
}
```

&emsp;&emsp;查询数据库操作：

``` cpp
QSqlQuery query;
/* 查询的结果可能不止一条记录，所以我们称之为结果集 */
query.exec ( "SELECT * FROM t_STUDENT" );

while ( query.next() ) {
    /* 取第i条记录第1个字段(从0开始计数)的结果 */
    QString name = query.value ( 0 ).toString();
    /* 取第i条记录第2个字段的结果 */
    int age = query.value ( 1 ).toInt();
    /* 处理name、age变量数据 */
}
```

&emsp;&emsp;`UPDATE`操作：

``` cpp
QSqlQuery query;
query.prepare ( "UPDATE employee SET salary = ? WHERE id = 1003" );
query.bindValue ( 0, 70000 );
query.exe();
```

&emsp;&emsp;`DELETE`操作：

``` cpp
QSqlQuery query;
query.exec ( "DELETE FROM employee WHERE id = 1007" );
```

- `seek(int n)`：`query`指向结果集的第`n`条记录，指定当前的位置。
- `first()`：`query`指向结果集的第一条记录。
- `last()`：`query`指向结果集的最后一条记录。
- `next()`：`query`指向下一条记录，每执行一次该函数，便指向相邻的下一条记录。
- `previous()`：`query`指向上一条记录，每执行一次该函数，便指向相邻的上一条记录。
- `record()`：获得现在指向的记录。
- `value(int n)`：获得属性的值，其中`n`表示查询的第`n`个属性。
- `int rowNum = query.at()`：获取`query`所指向的记录在结果集中的编号。
- `int fieldNo = query.record().indexOf("name")`：返回`name`的列号。
- `int columnNum = query.record().count()`：获取每条记录中属性(即`列`)的个数。

---

&emsp;&emsp;在`Qt`上使用`SQLite`时，如果第二次使用`QSqlDatabase::addDatabase`方法，可能会出现错误`QSqlDatabasePrivate::addDatabase: duplicate connection name 'qt_sql_default_connection', old connection removed`。
&emsp;&emsp;解决方法：先判断一下这个默认的连接名是否存在，如果不存在，则使用`addDatabase`方法；如果存在，则使用`database`方法。

``` cpp
QSqlDatabase db;

if ( QSqlDatabase::contains ( "qt_sql_default_connection" ) ) {
    db = QSqlDatabase::database ( "qt_sql_default_connection" );
} else {
    db = QSqlDatabase::addDatabase ( "QSQLITE" );
}
```

---

&emsp;&emsp;使用`QSqlQuery`时，最好这样写：

``` cpp
QSqlDatabase db;
QSqlQuery sql_query ( db );
sql_query.prepare ( create_sql );
```

否则可能会出现`Driver not loaded`的错误。