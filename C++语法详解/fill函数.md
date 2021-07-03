---
title: fill函数
categories: C++语法详解
date: 2018-12-27 10:16:53
---
&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
template <class ForwardIterator, class T>
void fill ( ForwardIterator first, ForwardIterator last, const T &val );
```

`fill`函数用于向容器中填充数据。

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

int main() {
    vector<int> myvector ( 8 ); /* myvector: 0 0 0 0 0 0 0 0 */
    fill ( myvector.begin(), myvector.begin() + 4, 5 ); /* myvector: 5 5 5 5 0 0 0 0 */
    fill ( myvector.begin() + 3, myvector.end() - 2, 8 ); /* myvector: 5 5 5 8 8 8 0 0 */
    cout << "myvector contains:";

    for ( vector<int>::iterator it = myvector.begin(); it != myvector.end(); ++it ) {
        cout << ' ' << *it;
    }

    cout << '\n';
    return 0;
}
```