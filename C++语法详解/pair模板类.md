---
title: pair模板类
categories: C++语法详解
abbrlink: af504dd1
date: 2018-12-27 09:51:53
---
&emsp;&emsp;`pair`是一种模板类型，其中包含两个数据值，两个数据的类型可以不同(也可以使用自己定义的`struct`数据结构)，基本的定义如下：<!--more-->

``` cpp
pair<int, string> a;
```

表示`a`中有两个类型，第一个元素是`int`型，第二个元素是`string`型。如果创建`pair`时没有对其进行初始化，则调用默认构造函数对其初始化。可以像下面一样在定义时直接对其初始化：

``` cpp
pair<string, string> a ( "James", "Joy" );
```

&emsp;&emsp;`pair`类型的使用比较繁琐，如果要定义多个相同的`pair`类型时，可以使用`typedef`简化声明：

``` cpp
typedef pair<string, string> author;
author pro ( "May", "Lily" );
author joye ( "James", "Joyce" );
```

&emsp;&emsp;对于`pair`类，由于它只有两个元素，分别名为`first`和`second`，因此直接使用普通的点操作符即可访问其成员：

``` cpp
pair<string, string> a ( "Lily", "Poly" );
string name;
name = a.second;
```

&emsp;&emsp;可以使用`make_pair`对已存在的两个数据构造一个新的`pair`类型：

``` cpp
int a = 8;
string m = "James";
pair<int, string> newone;
newone = make_pair ( a, m );
```

&emsp;&emsp;可以直接用标准输入流读入数据到`pair`对象中：

``` cpp
pair<string, string> next_auth;
​
while ( cin >> next_auth.first >> next_auth.second ) {
    /* ... */
}
```