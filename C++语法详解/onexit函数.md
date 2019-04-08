---
title: _onexit函数
date: 2019-02-06 10:51:24
tags:
---
&emsp;&emsp;`main`主函数执行完毕后，是否可能会再执行一段代码？答案是可以的，可以使用`_onexit`注册一个函数，它会在`main`之后执行。

``` cpp
_onexit ( int fun() );
```

其中函数`fun`必须是带有`int`类型返回值的无参数函数；`_onexit`包含在头文件`cstdlib`中。无论函数`_onexit`放到`main`中任意位置，它都是最后执行的。

``` cpp
#include <iostream>
#include <cstdlib>
​
using namespace std;
​
int func1(), func2(), func3();
​
int main ( int argc, char *argv[] ) {
    _onexit ( func2 );
    _onexit ( func1 );
    _onexit ( func3 );
    cout << "First Line" << endl;
    cout << "Second Line" << endl;
}
​
int func1() {
    cout << "fun1() executed!" << endl;
    return 0;
}
​
int func2() {
    cout << "fun2() executed!" << endl;
    return 0;
}
​
int func3() {
    cout << "fun3() executed!" << endl;
    return 0;
}
```

执行结果：

``` cpp
First Line
Second Line
fun3() executed!
fun1() executed!
fun2() executed!
```

由此可得，`_onexit`注册的函数在`main`中越靠后，则其执行顺序越靠前，类似于`栈`。