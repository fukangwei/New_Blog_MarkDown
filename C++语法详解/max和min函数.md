---
title: max和min函数
categories: C++语法详解
date: 2019-02-05 20:21:24
---
### max函数

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
template <class T> const T &max ( const T &a, const T &b );
```

函数返回`a`和`b`中的最大值。如果`a`和`b`相等，则返回`a`。

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    cout << "max(1, 2) == " << max ( 1, 2 ) << endl;
    cout << "max(2, 1) == " << max ( 2, 1 ) << endl;
    cout << "max('a', 'z') == " << max ( 'a', 'z' ) << endl;
    cout << "max(3.14, 2.73) == " << max ( 3.14, 2.73 ) << endl;
    return 0;
}
```

执行结果：

``` cpp
max(1, 2) == 2
max(2, 1) == 2
max('a', 'z') == z
max(3.14, 2.73) == 3.14
```

### min函数

&emsp;&emsp;函数原型如下：

``` cpp
template <class T> const T &min ( const T &a, const T &b );
```

函数返回`a`和`b`中的最小值。如果`a`和`b`相等，则返回`a`。

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    cout << "max(1, 2) == " << min ( 1, 2 ) << endl;
    cout << "max(2, 1) == " << min ( 2, 1 ) << endl;
    cout << "max('a', 'z') == " << min ( 'a', 'z' ) << endl;
    cout << "max(3.14, 2.73) == " << min ( 3.14, 2.73 ) << endl;
    return 0;
}
```

执行结果：

``` cpp
min(1, 2) == 1
min(2, 1) == 1
min('a', 'z') == a
min(3.14, 2.72) == 2.72
```