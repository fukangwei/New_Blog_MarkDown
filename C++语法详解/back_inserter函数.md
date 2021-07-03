---
title: back_inserter函数
categories: C++语法详解
date: 2018-12-27 10:35:19
---
&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
template <class Container>
back_insert_iterator<Container> back_inserter ( Container &x );
```

&emsp;&emsp;`back_inserter`常和`copy`一起使用，用于把一个容器的内容接到另一个容器的末尾：

``` cpp
#include <iostream>
#include <iterator>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    std::vector<int> foo, bar;

    for ( int i = 1; i <= 5; i++ ) {
        foo.push_back ( i );
        bar.push_back ( i * 10 );
    }

    copy ( bar.begin(), bar.end(), back_inserter ( foo ) );
    cout << "foo contains:";

    for ( vector<int>::iterator it = foo.begin(); it != foo.end(); ++it ) {
        cout << ' ' << *it;
    }

    cout << '\n';
    return 0;
}
```

执行结果：

``` cpp
foo contains: 1 2 3 4 5 10 20 30 40 50
```