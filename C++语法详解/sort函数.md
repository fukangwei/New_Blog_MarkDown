---
title: sort函数
date: 2021-02-21 07:56:53
categories: C++语法详解
---
### 基础用法

&emsp;&emsp;`sort`是`C++`中的一种排序函数：<!--more-->

``` cpp
void sort ( RandomAccessIterator first, RandomAccessIterator last, Compare comp );
```

- `first`：要排序的数组的起始地址。
- `last`：结束的地址，即最后一个数据后面的地址。
- `comp`：排序的方法，可以是升序，也可以是降序，默认是升序。

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    int a[10] = { 9, 6, 3, 8, 5, 2, 7, 4, 1, 0 };

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    cout << endl;
    sort ( a, a + 10 );

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    return 0;
}
```

&emsp;&emsp;如果要实现降序，则方法如下：

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

bool complare ( int a, int b ) {
    return a > b;
}

int main() {
    int a[10] = { 9, 6, 3, 8, 5, 2, 7, 4, 1, 0 };

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    cout << endl;
    sort ( a, a + 10, complare );

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    return 0;
}
```

### comp参数

&emsp;&emsp;`sort`的参数`comp`可以使用如下方式来设置排序方法：

- `less<数据类型>()`：从小到大排序。
- `greater<数据类型>()`：从大到小排序。

&emsp;&emsp;`less`方式如下：

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    int a[10] = { 9, 6, 3, 8, 5, 2, 7, 4, 1, 0 };

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    cout << endl;
    sort ( a, a + 10, less<int>() );

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    return 0;
}
```

&emsp;&emsp;`greater`方式如下：

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    int a[10] = { 9, 6, 3, 8, 5, 2, 7, 4, 1, 0 };

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    cout << endl;
    sort ( a, a + 10, greater<int>() );

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    return 0;
}
```