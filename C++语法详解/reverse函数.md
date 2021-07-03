---
title: reverse函数
categories: C++语法详解
date: 2018-12-27 10:45:07
---
&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
template <class BidirectionalIterator>
void reverse ( BidirectionalIterator first, BidirectionalIterator last );
```

`reverse`用于倒置容器中的数据。

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

int main() {
    vector<int> myvector;

    for ( int i = 1; i < 10; ++i ) {
        myvector.push_back ( i );
    }

    reverse ( myvector.begin(), myvector.end() );
    cout << "myvector contains:";

    for ( vector<int>::iterator it = myvector.begin(); it != myvector.end(); ++it ) {
        cout << ' ' << *it;
    }

    cout << '\n';
    return 0;
}
```

执行结果：

``` cpp
myvector contains: 9 8 7 6 5 4 3 2 1
```