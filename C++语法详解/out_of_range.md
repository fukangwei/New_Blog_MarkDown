---
title: out_of_range
date: 2021-07-02 19:52:24
categories: C++语法详解
---
&emsp;&emsp;`out_of_range`用于报告访问定义范围之外的元素所导致的错误：<!--more-->

``` cpp
#include <iostream>
#include <stdexcept>
#include <vector>

int main ( void ) {
    std::vector<int> myvector ( 10 );

    try {
        myvector.at ( 20 ) = 100;
    } catch ( const std::out_of_range& oor ) {
        std::cerr << "Out of Range error: " << oor.what() << '\n';
    }

    return 0;
}
```

执行结果：

``` cpp
Out of Range error: vector::_M_range_check: __n (which is 20) >= this->size() (which is 10)
```