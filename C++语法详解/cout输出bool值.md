---
title: cout输出bool值
date: 2021-03-16 19:08:17
categories: C++语法详解
---
&emsp;&emsp;使用`boolalpha`输出为`bool`类型，使用`noboolalpha`输出为数字类型：<!--more-->

``` cpp
#include <iostream>

using namespace std;

int main ( int argc, char* argv[] ) {
    bool test = true;
    cout << "the output is number " << test << endl;
    cout << "the output is bool(use boolalpha) " << boolalpha << test << endl;
    cout << "the output is number(use noboolalpha) " << noboolalpha << test << endl;
    return 0;
}
```

执行结果：

``` bash
the output is number 1
the output is bool(use boolalpha) true
the output is number(use noboolalpha) 1
```