---
title: sqlite3的C语言操作
date: 2019-01-17 14:16:50
categories: 数据库
---
&emsp;&emsp;下面的代码包含了创建数据表、添加数据以及查询数据等功能：<!--more-->

``` cpp
#include <stdio.h>
#include <sqlite3.h>

/* 查询的回调函数声明 */
int select_callback ( void *data, int col_count, char **col_values, char **col_Name );

int main ( int argc, char *argv[] ) {
    const char *sSQL1 = \
        "create table users(userid varchar(20) PRIMARY KEY, age int, birthday datetime);";
    char *pErrMsg = 0;
    int result = 0; /* 用于接收连接数据库操作返回的值 */
    sqlite3 *db = NULL; /* 声明一个连接数据库的指针对象 */
    int ret = sqlite3_open ( "./test.db", &db ); /* 使用sqlite3_open连接数据库 */

    if ( ret != SQLITE_OK ) {
        fprintf ( stderr, "无法打开数据库: %s", sqlite3_errmsg ( db ) );
        sqlite3_close ( db );
        return ( 1 );
    }

    printf ( "数据库连接成功!\n" );
    sqlite3_exec ( db, sSQL1, 0, 0, &pErrMsg ); /* 执行建表SQL */

    if ( ret != SQLITE_OK ) {
        fprintf ( stderr, "SQL error: %s\n", pErrMsg );
        sqlite3_free ( pErrMsg );
    }

    result = sqlite3_exec ( /* 执行插入记录SQL */
        db, "insert into users values('张三', 20, '2011-7-23');", 0, 0, &pErrMsg );

    if ( result == SQLITE_OK ) {
        printf ( "插入数据成功\n" );
    }

    result = sqlite3_exec (
        db, "insert into users values('李四',20,'2012-9-20');", 0, 0, &pErrMsg );

    if ( result == SQLITE_OK ) {
        printf ( "插入数据成功\n" );
    }

    printf ( "查询数据库内容\n" ); /* 查询数据表 */
    sqlite3_exec ( db, "select * from users;", select_callback, 0, &pErrMsg );
    sqlite3_close ( db ); /* 关闭数据库 */
    db = 0;
    printf ( "数据库关闭成功!\n" );
    return 0;
}

int select_callback ( void *data, int col_count, char **col_values, char **col_Name ) {
    int i; /* 每条记录回调一次该函数,有多少条就回调多少次 */

    for ( i = 0; i < col_count; i++ ) {
        printf ( "%s = %s\n", col_Name[i], col_values[i] == 0 ? "NULL" : col_values[i] );
    }

    return 0;
}
```

编译代码需要使用命令：

``` bash
gcc main.c -o main -lsqlite3
```

&emsp;&emsp;编译该程序时有可能出现`fatal error: sqlite3.h: No such file or directory`的问题，原因是系统没有安装函数库。执行下面语句即可解决：

``` bash
sudo apt-get install libsqlite3-dev
```

当用交叉编译器编译的时候，也会出现找不到`sqlite3.h`头文件的情况，解决方法是把`sqlite3.h`这个头文件放到交叉编译工具目录的`include`目录下。
&emsp;&emsp;对于`sqlite3_open`函数，若指定的数据库存在则进行连接，否则创建一个新的数据库。

---

&emsp;&emsp;基础代码如下：

``` cpp
#include <stdio.h>
#include <stdlib.h>
#include <string.h>
#include "sqlite3.h"

sqlite3 *db = NULL;
static int sn = 0;

void create_table ( char *filename ) {
    char *sql;
    char *zErrMsg = 0;
    int rc;
    rc = sqlite3_open ( filename, &db );
    // rc = sqlite3_open_v2 ( filename, &db, SQLITE_OPEN_READWRITE | SQLITE_OPEN_CREATE, NULL );
    if ( rc ) {
        fprintf ( stderr, "can't open database%s\n", sqlite3_errmsg ( db ) );
        sqlite3_close ( db );
    }

    sql = "CREATE TABLE save_data(num integer primary key, id int, data text, time text)";
    sqlite3_exec ( db, sql, 0, 0, &zErrMsg );
}

void close_table ( void ) {
    sqlite3_close ( db );
}

void insert_record ( char *table, int id, char *data, char *time ) {
    char *sql;
    char *zErrMsg = NULL;
    sql = sqlite3_mprintf ( "insert into %s values(null,%d,'%s','%s')", table, id, data, time );
    sqlite3_exec ( db, sql, 0, 0, &zErrMsg );
    sqlite3_free ( sql );
}

int sqlite_callback ( void *userData, int numCol, char **colData, char **colName ) {
    int i, offset = 0;
    char *buf, *tmp;
    buf = ( char * ) malloc ( 40 * sizeof ( char ) );
    tmp = buf;
    memset ( buf, 0, 40 );

    for ( i = 1; i < numCol; i++ ) {
        buf = buf + offset;
        sprintf ( buf, "%s ", colData[i] );
        /* it's need one place for put a blank, so the lenght add 1 */
        offset = strlen ( colData[i] ) + 1;
    }

    printf ( "%.4d. %s \n", ++sn, tmp );
    free ( tmp );
    tmp = NULL;
    buf = NULL;
    return 0;
}

void search_all ( char *table ) {
    char *sql;
    char *zErrMsg = 0;
    sn = 0;
    sql = sqlite3_mprintf ( "select * from %s", table );
    sqlite3_exec ( db, sql, &sqlite_callback, 0, &zErrMsg );
    sqlite3_free ( sql );
}

void search_by_id ( char *table, char *id ) {
    char *sql;
    char *zErrMsg = 0;
    sn = 0;
    sql = sqlite3_mprintf ( "select * from %s where id=%s", table, id );
    sqlite3_exec ( db, sql, &sqlite_callback, 0, &zErrMsg );
    sqlite3_free ( sql );
}

void delete_by_id ( char *table, char *id ) {
    int rc ;
    char *sql;
    char *zErrMsg = 0;
    sql = sqlite3_mprintf ( "delete from %s where id=%s", table, id );
    rc = sqlite3_exec ( db, sql, 0, 0, &zErrMsg );
    sqlite3_free ( sql );
}

void delete_all ( char *table ) {
    char *sql = NULL;
    char *zErrMsg = NULL;
    sql = sqlite3_mprintf ( "delete from %s", table );
    sqlite3_exec ( db, sql, 0, 0, &zErrMsg );
    sqlite3_free ( sql );
}

int main ( int agrc, char *argv[] ) {
    char *filename = "data.db";
    int i;
    create_table ( filename );

    for ( i = 0 ; i < 10; i++ ) {
        insert_record ( "save_data", 2000, "5678", "2012-03-12 09:43:56" );
        insert_record ( "save_data", 2001, "5678", "2012-03-12 09:43:56" );
        insert_record ( "save_data", 2002, "5678", "2012-03-12 09:43:56" );
        insert_record ( "save_data", 2003, "5678", "2012-03-12 09:43:56" );
        insert_record ( "save_data", 2004, "5678", "2012-03-12 09:43:56" );
        insert_record ( "save_data", 2005, "5678", "2012-03-12 09:43:56" );
        insert_record ( "save_data", 2006, "5678", "2012-03-12 09:43:56" );
        insert_record ( "save_data", 2007, "5678", "2012-03-12 09:43:56" );
    }

    search_all ( "save_data" );
    close_table();
    return 0;
}
```


---

### sqlite3使用回调函数

&emsp;&emsp;`sqlite3_exe`函数原型如下：

``` cpp
int sqlite3_exec (
    sqlite3 *db,     /* An open database */
    const char *sql, /* SQL to be executed */
    sqlite_callback, /* Callback function 回调函数名 */
    void *data,      /* 1st argument to callback function 传给回调函数的第一个参数*/
    char **errmsg    /* Error msg written here */
);
```

`sqlite3_exec`包含一个回调机制，提供了一种从`SELECT`语句得到结果的方法。`sqlite3_exec`函数第`3`个参数是一个指向回调函数的指针。如果提供了该函数，`SQLite`则会在执行`SELECT`语句时为遇到的每一条记录都调用回调函数。回调函数的格式如下：

``` cpp
int sqlite_callback (
    void *pv,    /* 由sqlite3_exec的第四个参数传递而来 */
    int argc,    /* 表的列数 */
    char **argv, /* 指向查询结果的指针数组，可以由sqlite3_column_text得到 */
    char **col   /* 指向表头名的指针数组，可以由sqlite3_column_name得到 */
);
```

返回值`1`为中断查找，`0`为继续列举查询到的数据。
&emsp;&emsp;假设有如下数据库：

ID  | NAME  | ADDRESS    | AGE
----|-------|------------|----
`1` | `YSP` | `ShangHai` | `22`
`2` | `HHB` | `ShangHai` | `25`

对数据库进行查询，查询到第一条记录，回调函数被调用一次：

``` cpp
ncols = 4 (总共4个字段)
 values[0]:  1;  values[1]:  YSP;  values[2]: ShangHai;  values[3]: 22
headers[0]: ID; headers[1]: NAME; headers[2]:  ADDRESS; headers[3]: AGE
```

查询到第二条记录，回调函数被调用第二次：

``` cpp
ncols = 4 (总共4个字段)
 values[0]:  1;  values[1]:  HHB;  values[2]: ShangHai;  values[3]: 25
headers[0]: ID; headers[1]: NAME; headers[2]:  ADDRESS; headers[3]: AGE
```


---

### sqlite3_get_table函数

&emsp;&emsp;通常情况下，实现查询数据库是让`sqlite3_exec`使用回调函数来执行`select`操作。还有一个方法可以直接查询而不需要回调，就是通过`sqlite3_get_table`函数：

``` cpp
int sqlite3_get_table (
    sqlite3 *db,      /* 打开的数据库对象指针          */
    const char *sql,  /* 要查询的sql语句              */
    char *** resultp, /* 查询结果                     */
    int *nrow,        /* 查询出多少条记录(即查出多少行) */
    int *ncolumn,     /* 多少个字段(多少列)            */
    char **errmsg     /* 错误信息                     */
);
```

第`3`个参数是查询结果，它依然是一维数组(不要认为是二维数组，更不要认为是三维数组)。它内存布局是：第一行是字段名称，后面是紧接着是每个字段的值。

``` cpp
#include <unistd.h>
#include <stdio.h>
#include <stdlib.h>
#include <sqlite3.h>

int main ( int argc, char **argv ) {
    sqlite3 *db;
    char **dbResult;
    char *errmsg;
    int nRow, nColumn;
    int index = 0;
    int i, j, rc;

    if ( argc != 2 ) {
        fprintf ( stderr, "Usage: %s DATABASE \n", argv[0] );
        exit ( 1 );
    }

    rc = sqlite3_open ( argv[1], &db );

    if ( rc != SQLITE_OK ) {
        fprintf ( stderr, "Can't open database: %s\n", sqlite3_errmsg ( db ) );
        sqlite3_close ( db );
        exit ( 1 );
    }

    rc = sqlite3_get_table ( db, "select * from users", &dbResult, &nRow, &nColumn, &errmsg );

    if ( rc == SQLITE_OK ) {
        printf ( "表格共%d 记录!\n", nRow );
        printf ( "表格共%d 列!\n", nColumn );
        /* 前两个字段为字段名field0和field1，后面是row[0][0]、row[0][1]、row[1][0]、
           row[1][1]等。它是一维数组，不是二维数组。注意，第0和第1列的值为字段名，然后才是字段值 */
        printf ( "字段名|字段值\n" );
        printf ( "%s | %s\n", dbResult[0], dbResult[1] );
        printf ( "--------------------------------\n" );
        index = nColumn; /* 字段值从index开始 */

        for ( i = 0; i < nRow ; i++ ) {
            for ( j = 0 ; j < nColumn; j++ ) {
                printf ( "%-5s ", dbResult[index++] );
            }

            printf ( "\n" );
        }

        printf ( "--------------------------------\n" );
    }

    /* 到这里，不论数据库查询是否成功，使用sqlite提供的功能来释放内存 */
    sqlite3_free_table ( dbResult );
    sqlite3_close ( db );
    return 0;
}
```