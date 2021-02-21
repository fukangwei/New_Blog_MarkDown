---
title: sort函数
date: 2021-02-21 07:56:53
categories: C++语法详解
---
&emsp;&emsp;`sort`是`c++`中的一种排序函数，时间复杂度为`n * log2(n)`，执行效率较高。<!--more-->
&emsp;&emsp;`sort`函数有`3`个参数：

``` cpp
void sort ( RandomAccessIterator first, RandomAccessIterator last, Compare comp );
```

- `first`：要排序的数组的起始地址。
- `last`：结束的地址，即最后一个数据的后一个数据的地址。
- `comp`：排序的方法，可以是从升序也可是降序。如果第三个参数不写，则默认的排序方法是从小到大排序。

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    int a[10] = {9, 6, 3, 8, 5, 2, 7, 4, 1, 0};

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

&emsp;&emsp;如果要实现从大到小的排序，该怎么办？如下所示：

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

bool complare ( int a, int b ) {
    return a > b;
}

int main() {
    int a[10] = {9, 6, 3, 8, 5, 2, 7, 4, 1, 0};

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

&emsp;&emsp;`sort`函数的第三个参数可以用如下的方式来设置排序原则：

- `less<数据类型>()`：从小到大排序。
- `greater<数据类型>()`：从大到小排序。

&emsp;&emsp;`less`方式如下：

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    int a[10] = {9, 6, 3, 8, 5, 2, 7, 4, 1, 0};

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
    int a[10] = {9, 6, 3, 8, 5, 2, 7, 4, 1, 0};

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

&emsp;&emsp;利用`sort`函数还可以实现对字符进行排序：

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    char a[11] = "asdfghjklk";

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    cout << endl;
    sort ( a, a + 10, greater<char>() );

    for ( int i = 0; i < 10; i++ ) {
        cout << a[i] << " ";
    }

    return 0;
}
```