---
title: set用法
categories: C++语法详解
date: 2019-02-06 11:14:55
---
### 关于set

&emsp;&emsp;`C++`的`STL`之所以得到广泛的赞誉，不只是提供了像`vector`、`string`和`list`等方便的容器，更重要的是`STL`封装了许多复杂的数据结构算法和大量常用数据结构操作，例如`vector`封装了数组，`list`封装了链表，`map`和`set`封装了二叉树等。在封装这些数据结构的时候，`STL`按照程序员的使用习惯，以成员函数方式提供的常用操作，例如插入、排序、删除、查找等，让用户在`STL`使用过程中，并不会感到陌生。<!--more-->
&emsp;&emsp;关于`set`，必须说明的是`set`关联式容器。`set`作为一个容器也是用来存储同一数据类型的数据类型，并且能从一个数据集合中取出数据，在`set`中，每个元素的值都唯一，而且系统能根据元素的值自动进行排序。应该注意的是`set`中数元素的值不能直接被改变。`C++`的`STL`中标准关联容器`set`、`multiset`、`map`和`multimap`内部采用的就是一种非常高效的平衡检索二叉树，即红黑树(`Red-Black Tree`)。`RB`树的统计性能要好于一般平衡二叉树，所以被`STL`选择作为了关联容器的内部结构。

### set中常用的方法

&emsp;&emsp;常用的方法如下：

方法         | 说明
-------------|-----
`begin()`    | 返回`set`容器的第一个元素
`end()`      | 返回`set`容器的最后一个元素
`clear()`    | 删除`set`容器中的所有的元素
`empty()`    | 判断`set`容器是否为空
`max_size()` | 返回`set`容器可能包含的元素最大个数
`size()`     | 返回当前`set`容器中的元素个数

&emsp;&emsp;`insert(key_value)`将`key_value`插入到`set`中，返回值是`pair<set<int>::iterator, bool>`，`bool`标志着插入是否成功，而`iterator`代表插入的位置，若`key_value`已经在`set`中，则`iterator`表示`key_value`在`set`中的位置。

``` cpp
#include <iostream>
#include <set>

using namespace std;

int main() {
    set<int> s;
    s.insert(1);
    s.insert(2);
    s.insert(3);
    s.insert(1);
    cout << "set的size值为: " << s.size() << endl;
    cout << "set的maxsize的值为: " << s.max_size() << endl;
    cout << "set中的第一个元素是: " << *s.begin() << endl;
    cout << "set中的最后一个元素是: " << *s.end() << endl;
    s.clear();

    if (s.empty()) {
        cout << "set为空!!!" << endl;
    }

    cout << "set的size值为: " << s.size() << endl;
    cout << "set的maxsize的值为: " << s.max_size() << endl;
    return 0;
}
```

执行结果：

``` cpp
set的size值为: 3
set的maxsize的值为: 461168601842738790
set中的第一个元素是: 1
set中的最后一个元素是: 3
set为空!!!
set的size值为: 0
set的maxsize的值为: 461168601842738790
```

&emsp;&emsp;`count`用来查找`set`中某个某个键值出现的次数。这个函数在`set`并不是很实用，因为一个键值在`set`只可能出现`0`或`1`次，这样就变成了判断某一键值是否在`set`出现过了：

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
    cout << "set中1出现的次数是: " << s.count ( 1 ) << endl;
    cout << "set中4出现的次数是: " << s.count ( 4 ) << endl;
    return 0;
}
```

执行结果：

``` cpp
set中1出现的次数是: 1
set中4出现的次数是: 0
```

&emsp;&emsp;使用`insert`将元素插入到集合中时，集合会根据设定的比较函数将该元素放到相应的节点上。在定义集合时，如果没有指定比较函数，那么采用默认的比较函数(按键值又小到大的顺序插入元素)。
&emsp;&emsp;如果元素不是结构体，那么可以这样编写比较函数：

``` cpp
#include <iostream>
#include <set>
#include <iterator>

using namespace std;

struct myComp { /* 自定义比较函数myComp，重载操作符“()” */
    bool operator() ( const int &a, const int &b ) {
        return a > b; /* 从大到小排序 */
        // return a < b; /* 从小到大排序 */
    }
};

int main() {
    set<int, myComp> s1;

    for ( int i = 1; i < 6; i++ ) {
        s1.insert ( i * i );
    }

    s1.insert ( 8 );
    set<int, myComp>::iterator it = s1.begin();

    for ( ; it != s1.end(); it++ ) {
        cout << *it << " ";
    }

    cout << endl;
    return 0;
}
```

执行结果：

``` cpp
25 16 9 8 4 1
```

&emsp;&emsp;如果元素是结构体，那么可以直接把比较函数写在结构体内：

``` cpp
#include <iostream>
#include <set>
#include <iterator>
#include <string>

using namespace std;

struct Info {
    string name;
    float score;
    /* 重载操作符“<”，自定义排序规则 */
    bool operator< ( const Info &a ) const {
        // return a.score < score; /* 按score由大到小排列 */
        return a.score > score; /* 由小到大排列 */
    }
};

int main() {
    set<Info> s;
    Info info;
    info.name = "Messi";
    info.score = 8.5;
    s.insert ( info );
    info.name = "Ronae";
    info.score = 9.0;
    s.insert ( info );
    info.name = "My";
    info.score = 7.0;
    s.insert ( info );
    info.name = "Perno";
    info.score = 8.0;
    s.insert ( info );
    info.name = "Arzar";
    info.score = 8.5;
    s.insert ( info );
    set<Info>::iterator it;

    for ( it = s.begin(); it != s.end(); it++ ) {
        cout << it->name << ": " << ( *it ).score << endl;
    }

    return 0;
}
```

执行结果：

``` cpp
My: 7
Perno: 8
Messi: 8.5
Ronae: 9
```