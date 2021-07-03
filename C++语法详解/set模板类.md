---
title: set模板类
categories: C++语法详解
date: 2019-02-06 11:14:55
---
&emsp;&emsp;`set`用来存储同一数据类型的元素，每个元素的值都是唯一的，而且系统能根据元素的值自动进行排序。<!--more-->

### insert

&emsp;&emsp;`insert`用于向`set`中插入数据：

``` cpp
#include <iostream>
#include <set>

using namespace std;

int main() {
    set<int> s;
    s.insert ( 1 );
    s.insert ( 2 );
    s.insert ( 3 );
    s.insert ( 1 );
    cout << "set的size值为：" << s.size() << endl;
    cout << "set的max_size的值为：" << s.max_size() << endl;
    cout << "set中的第一个元素是：" << *s.begin() << endl;
    s.clear();

    if ( s.empty() ) {
        cout << "set为空！" << endl;
    }

    cout << "set的size值为：" << s.size() << endl;
    cout << "set的max_size的值为：" << s.max_size() << endl;
    return 0;
}
```

执行结果：

``` cpp
set的size值为：3
set的max_size的值为：214748364
set中的第一个元素是：1
set为空！
set的size值为：0
set的max_size的值为：214748364
```

### count

&emsp;&emsp;`count`用来查找`set`中某个值出现的次数，经常用于判断`set`中是否有某个值：

``` cpp
#include <iostream>
#include <set>

using namespace std;

int main() {
    set<int> s;
    s.insert ( 1 );
    s.insert ( 2 );
    s.insert ( 3 );
    s.insert ( 1 );
    cout << "set中1出现的次数是：" << s.count ( 1 ) << endl;
    cout << "set中4出现的次数是：" << s.count ( 4 ) << endl;
    return 0;
}
```

执行结果：

``` cpp
set中1出现的次数是：1
set中4出现的次数是：0
```