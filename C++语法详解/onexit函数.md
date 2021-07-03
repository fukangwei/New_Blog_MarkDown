---
title: _onexit函数
categories: C++语法详解
date: 2019-02-06 10:51:24
---
&emsp;&emsp;`main`函数执行完后，是否可能会再执行一段代码？答案是可以的，使用`_onexit`注册一个函数，它会在`main`之后执行。<!--more-->

``` cpp
_onexit ( int fun() );
```

其中`fun`必须是带有`int`类型返回值的无参数函数。

``` cpp
#include <iostream>
#include <cstdlib>

using namespace std;

int func1() {
    cout << "fun1() executed!" << endl;
    return 0;
}

int func2() {
    cout << "fun2() executed!" << endl;
    return 0;
}

int main ( int argc, char* argv[] ) {
    _onexit ( func2 );
    _onexit ( func1 );
    cout << "First Line" << endl;
    cout << "Second Line" << endl;
}
```

执行结果：

``` cpp
First Line
Second Line
fun1() executed!
fun2() executed!
```

&emsp;&emsp;`_onexit`注册的函数在`main`中越靠后，则其执行顺序越靠前，类似于`栈`。