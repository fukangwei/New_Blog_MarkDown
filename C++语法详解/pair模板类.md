---
title: pair模板类
categories: C++语法详解
date: 2018-12-27 09:51:53
---
&emsp;&emsp;`pair`是一种模板类型，其中包含两个数据值。<!--more-->
&emsp;&emsp;举个例子，`a`的第一个元素是`int`型，第二个元素是`string`型：

``` cpp
pair<int, string> a;
```

### 初始化

&emsp;&emsp;可以在定义时直接对`pair`变量进行初始化：

``` cpp
pair<string, string> a ( "James", "Joy" );
```

&emsp;&emsp;可以使用`make_pair`将已存在的两个数据构造成一个`pair`变量：

``` cpp
int a = 8;
string m = "James";
pair<int, string> newone;
newone = make_pair ( a, m );
```

### typedef

&emsp;&emsp;使用`typedef`来简化对`pair`类型的声明：

``` cpp
typedef pair<string, string> author;
author pro ( "May", "Lily" );
author joye ( "James", "Joyce" );
```

### first和second

&emsp;&emsp;`pair`类只有`2`个元素，分别名为`first`和`second`：

``` cpp
pair<string, string> a ( "Lily", "Poly" );
string name;
name = a.second;
```

### 标准输入流

&emsp;&emsp;可以直接用标准输入流将数据读取到`pair`变量中：

``` cpp
pair<string, string> next_auth;

while ( cin >> next_auth.first >> next_auth.second ) {
    /* ... */
}
```