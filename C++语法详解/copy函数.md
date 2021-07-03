---
title: copy函数
categories: C++语法详解
date: 2019-02-05 18:23:20
---
&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
template <class InputIterator, class OutputIterator>
OutputIterator copy ( InputIterator first, InputIterator last, OutputIterator result );
```

&emsp;&emsp;`copy`函数常用于复制数据到容器中：

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

int main() {
    int myints[] = { 10, 20, 30, 40, 50, 60, 70 };
    vector<int> myvector ( 7 );
    copy ( myints, myints + 7, myvector.begin() );
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
myvector contains: 10 20 30 40 50 60 70
```