---
title: unique和unique_copy函数
categories: C++语法详解
date: 2019-02-05 20:34:59
---
### unique

&emsp;&emsp;`unique`的功能是去除相邻的重复元素，只保留一个：<!--more-->

``` cpp
unique ( first, last, compare );
```

注意，`unique`函数也并非真正地删除了元素，一般要与`erase`或`resize`配合使用。
&emsp;&emsp;删除字符串中相邻的重复元素，并打印字符串：

``` cpp
#include <iostream>
#include <algorithm>
#include <string>

using namespace std;

int main ( void ) {
    string str;
    cin >> str;
    str.erase ( unique ( str.begin(), str.end() ), str.end() );
    // str.resize ( unique ( str.begin(), str.end() ) - str.begin() );
    cout << str << endl;
    return 0;
}
```

输入`abbbccbba`，输出`abcba`。

### unique_copy

&emsp;&emsp;`unique_copy`与`unique`的唯一区别：`unique_copy`会将去除相邻重复元素的结果保存在另外一个容器中。

``` cpp
#include <iostream>
#include <algorithm>
#include <string>

using namespace std;

int main ( void ) {
    string first, second;
    cin >> first;
    second.resize ( first.size() );
    second.resize ( unique_copy ( first.begin(), first.end(), second.begin() ) - second.begin() );
    cout << second << endl;
    return 0;
}
```

输入`abbbccbba`，输出`abcba`。