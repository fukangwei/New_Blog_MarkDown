---
title: map容器
categories: C++语法详解
date: 2019-02-07 20:47:32
---
&emsp;&emsp;1. 构造最基本的`map`容器：<!--more-->

``` cpp
map<string, int>  mapstring;
map<int, string>  mapint;
map<sring, char>  mapstring;
map<char, string> mapchar;
map<char, int>    mapchar;
map<int, char>    mapint;
```

&emsp;&emsp;2. 向`map`中添加数据：

``` cpp
map<int, string> maplive;
maplive.insert ( pair<int, string> ( 102, "aclive" ) );
maplive.insert ( map<int, string>::value_type ( 321, "hai" ) );
maplive[112] = "April"; /* map中最常用的插入操作 */
```

&emsp;&emsp;3. 在`map`中查找元素。`find`函数返回一个迭代器指向键值为`key`的元素；如果没找到，就返回指向`map`尾部的迭代器：

``` cpp
map<int, string>::iterator l_it;;
l_it = maplive.find ( 112 );

if ( l_it == maplive.end() ) {
    cout << "we do not find 112" << endl;
} else {
    cout << "wo find 112" << endl;
}
```

&emsp;&emsp;4. 删除`map`中的元素：

``` cpp
/* 如果需要删除key值为112的元素 */
map<int, string>::iterator l_it;;
l_it = maplive.find ( 112 );

if ( l_it == maplive.end() ) {
    cout << "we do not find 112" << endl;
} else {
    maplive.erase ( l_it ); /* delete 112 */
}
```

&emsp;&emsp;5. `map`中的`swap`用法。`map`中的`swap`不是一个容器中的元素交换，而是两个容器交换：

``` cpp
#include <map>
#include <iostream>

using namespace std;

int main( ) {
    map <int, int> m1, m2, m3;
    map <int, int>::iterator m1_Iter;
    /*----------------------------------------*/
    m1.insert ( pair <int, int> ( 1, 10 ) );
    m1.insert ( pair <int, int> ( 2, 20 ) );
    m1.insert ( pair <int, int> ( 3, 30 ) );
    /*----------------------------------------*/
    m2.insert ( pair <int, int> ( 10, 100 ) );
    m2.insert ( pair <int, int> ( 20, 200 ) );
    /*----------------------------------------*/
    m3.insert ( pair <int, int> ( 30, 300 ) );
    cout << "The original map m1 is:";

    for ( m1_Iter = m1.begin( ); m1_Iter != m1.end( ); m1_Iter++ ) {
        cout << " " << m1_Iter->second;
    }

    cout << endl;
    // This is the member function version of swap
    // m2 is said to be the argument map; m1 the target map
    m1.swap ( m2 );
    cout << "After swapping with m2, map m1 is:";

    for ( m1_Iter = m1.begin( ); m1_Iter != m1.end( ); m1_Iter++ ) {
        cout << " " << m1_Iter -> second;
    }

    cout << endl;
    cout << "After swapping with m2, map m2 is:";

    for ( m1_Iter = m2.begin( ); m1_Iter != m2.end( ); m1_Iter++ ) {
        cout << " " << m1_Iter -> second;
    }

    cout << endl;
    // This is the specialized template version of swap
    swap ( m1, m3 );
    cout << "After swapping with m3, map m1 is:";

    for ( m1_Iter = m1.begin( ); m1_Iter != m1.end( ); m1_Iter++ ) {
        cout << " " << m1_Iter -> second;
    }

    cout << endl;
}
```

&emsp;&emsp;6. `map`的`sort`问题。`map`中的元素是自动按`key`升序排序，所以不能对`map`用`sort`函数：

``` cpp
#include <map>
#include <iostream>

using namespace std;

int main( ) {
    map <int, int> m1;
    map <int, int>::iterator m1_Iter;
    m1.insert ( pair <int, int> ( 1, 20 ) );
    m1.insert ( pair <int, int> ( 4, 40 ) );
    m1.insert ( pair <int, int> ( 3, 60 ) );
    m1.insert ( pair <int, int> ( 2, 50 ) );
    m1.insert ( pair <int, int> ( 6, 40 ) );
    m1.insert ( pair <int, int> ( 7, 30 ) );
    cout << "The original map m1 is:" << endl;

    for ( m1_Iter = m1.begin( ); m1_Iter != m1.end( ); m1_Iter++ ) {
        cout << m1_Iter->first << " " << m1_Iter->second << endl;
    }
}
```

&emsp;&emsp;7. `map`的基本操作函数：

函数            | 说明
----------------|-----
`begin`         | 返回指向`map`头部的迭代器
`clear`         | 删除所有元素
`count`         | 返回指定元素出现的次数
`empty`         | 如果`map`为空则返回`true`
`end`           | 返回指向`map`末尾的迭代器
`equal_range`   | 返回特殊条目的迭代器对
`erase`         | 删除一个元素
`find`          | 查找一个元素
`get_allocator` | 返回`map`的配置器
`insert`        | 插入元素
`key_comp`      | 返回比较元素`key`的函数
`lower_bound`   | 返回键值`>=`给定元素的第一个位置
`max_size`      | 返回可以容纳的最大元素个数
`rbegin`        | 返回一个指向`map`尾部的逆向迭代器
`rend`          | 返回一个指向`map`头部的逆向迭代器
`size`          | 返回`map`中元素的个数
`swap`          | 交换两个`map`
`upper_bound`   | 返回键值`>`给定元素的第一个位置
`value_comp`    | 返回比较元素`value`的函数

### C++中map的使用注意事项

&emsp;&emsp;在`map`中，由`key`查找`value`时，首先要判断`map`中是否包含`key`。如果不检查，直接返回`map[key]`，可能会出现意想不到的行为。如果`map`包含`key`，没有问题；如果`map`不包含`key`，使用下标有一个危险的副作用：它会在`map`中插入一个`key`的元素，`value`取默认值并返回`value`。也就是说，`map[key]`不可能返回`null`。
&emsp;&emsp;`map`提供了两种方式，查看是否包含`key`，即`m.count(key)`和`m.find(key)`。

- `m.count(key)`：由于`map`不包含重复的`key`，因此`m.count(key)`取值为`0`，或者`1`，表示是否包含。
- `m.find(key)`：返回迭代器，判断是否存在。

&emsp;&emsp;对于下面的场景，存在`key`就使用，否则返回`null`。有下面两种写法：

``` cpp
/*------ 方法1 ------*/
if ( m.count ( key ) > 0 ) {
    return m[key];
}

return null;

/*------ 方法2 ------*/
iter = m.find ( key );

if ( iter != m.end() ) {
    return iter->second;
}

return null;
```

这里需要注意：前一种方法很直观，但是效率差很多，因为前面的方法需要执行两次查找，因此推荐使用后一种方法。
&emsp;&emsp;对于`STL`中的容器，有泛型算法`find(begin，end，target)`查找目标，`map`还提供了一个成员方法`find(key)`。
&emsp;&emsp;`map`是模板，一个`map`变量`key`和`value`两个值，`m_map->first`可以取得`key`值，`m_map->second`可以取得`value`值；`key`的值不能修改，可以修改`value`的值。`map[key]`返回的是`key`对应的`value`变量，它是一个左右值，可以对`map[key]`使用`++`或`--`操作：

``` cpp
#include <map>
#include <iterator>
#include <iostream>
#include <algorithm>

using namespace std;

map<string, int> histogtram;

void record ( const string &s ) {
    histogtram[s]++; /* 记录字符串s出现的次数 */
}

void print ( const pair<const string, int> &r ) {
    cout << r.first << " " << r.second << endl;
}

int main ( int argc, char const *argv[] ) {
    istream_iterator<string> ii ( cin ); /* 流的输入迭代器 */
    istream_iterator<string> eos; /* 输入的哨兵 */
    for_each ( ii, eos, record );
    for_each ( histogtram.begin(), histogtram.end(), print );
    return 0;
}
```