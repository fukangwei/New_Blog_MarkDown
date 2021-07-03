---
title: find_if和count_if函数
categories: C++语法详解
date: 2019-02-05 21:45:44
---
### find_if

&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
template <class InputIterator, class UnaryPredicate>
InputIterator find_if ( InputIterator first, InputIterator last, UnaryPredicate pred );
```

`find_if`用于在容器中查找符合要求的第一个数据。

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

bool IsOdd ( int i ) {
    return ( ( i % 2 ) == 1 );
}

int main() {
    vector<int> myvector;
    myvector.push_back ( 10 );
    myvector.push_back ( 25 );
    myvector.push_back ( 40 );
    myvector.push_back ( 55 );
    vector<int>::iterator it = std::find_if ( myvector.begin(), myvector.end(), IsOdd );
    cout << "The first odd value is " << *it << '\n';
    return 0;
}
```

执行结果：

``` cpp
The first odd value is 25
```

### count_if

&emsp;&emsp;函数原型如下：

``` cpp
template <class InputIterator, class UnaryPredicate>
typename iterator_traits<InputIterator>::difference_type
count_if ( InputIterator first, InputIterator last, UnaryPredicate pred );
```

`count_if`用于统计容器中符号要求的数据数量。

``` cpp
#include <iostream>
#include <algorithm>
#include <vector>

using namespace std;

bool IsOdd ( int i ) {
    return ( ( i % 2 ) == 1 );
}

int main() {
    vector<int> myvector;

    for ( int i = 1; i < 10; i++ ) {
        myvector.push_back ( i ); /* myvector: 1 2 3 4 5 6 7 8 9 */
    }

    int mycount = count_if ( myvector.begin(), myvector.end(), IsOdd );
    cout << "myvector contains " << mycount << " odd values\n";
    return 0;
}
```

执行结果：

``` cpp
myvector contains 5 odd values
```