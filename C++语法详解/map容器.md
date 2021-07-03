---
title: map容器
categories: C++语法详解
date: 2019-02-07 20:47:32
---
&emsp;&emsp;`map`是典型的`key-value`数据结构。<!--more-->

### 构造map容器

&emsp;&emsp;构造`map`容器，并添加数据的方法如下：

``` cpp
map<int, string> maplive;
maplive.insert ( pair<int, string> ( 102, "aclive" ) );
maplive.insert ( map<int, string>::value_type ( 321, "hai" ) );
maplive[112] = "April"; /* map中最常用的插入操作 */
```

### 查找元素

&emsp;&emsp;`find`函数用于在`map`中查找元素：

- 如果找到，则返回一个指向该元素的迭代器。
- 如果没找到，就返回指向`map`尾部的迭代器。

``` cpp
map<int, string>::iterator l_it;
l_it = maplive.find ( 112 );

if ( l_it == maplive.end() ) {
    cout << "we do not find 112" << endl;
} else {
    cout << "we find 112" << endl;
}
```

### 删除元素

&emsp;&emsp;删除`map`中的元素使用函数`erase`：

``` cpp
/* 删除key值为112的元素 */
map<int, string>::iterator l_it;
l_it = maplive.find ( 112 );

if ( l_it == maplive.end() ) {
    cout << "we do not find 112" << endl;
} else {
    maplive.erase ( l_it ); /* delete 112 */
}
```

### first和second

&emsp;&emsp;一个`map`变量中有成员`first`和`second`，分别对应该变量的`key`和`value`：

``` cpp
#include <iostream>
#include <algorithm>
#include <map>

using namespace std;

int main() {
    map<int, string> maplive;
    map <int, string>::iterator m_Iter;
    maplive.insert ( pair<int, string> ( 102, "aclive" ) );
    maplive.insert ( map<int, string>::value_type ( 321, "hai" ) );
    maplive[112] = "April";

    for ( m_Iter = maplive.begin(); m_Iter != maplive.end(); m_Iter++ ) {
        cout << "first is "  << m_Iter->first << ", "
             << "second is " << m_Iter->second << endl;
    }

    return 0;
}
```

执行结果：

``` cpp
first is 102, second is aclive
first is 112, second is April
first is 321, second is hai
```

&emsp;&emsp;可以看出，`map`中的元素是按`key`升序自动排序。