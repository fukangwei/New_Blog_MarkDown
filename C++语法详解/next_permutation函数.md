---
title: next_permutation函数
categories: C++语法详解
date: 2018-12-27 10:28:40
---
&emsp;&emsp;`next_permutation`函数是按照字典顺序，生成一个序列的所有排列：<!--more-->

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    int ans[] = { 1, 2, 3 };
    sort ( ans, ans + 3 ); /* 这个sort可以不用，因为{1, 2, 3}已经排好序了 */

    do {
        for ( int i = 0; i < 3; ++i ) {
            cout << ans[i] << " ";
        }

        cout << endl;
    } while ( next_permutation ( ans, ans + 3 ) );

    return 0;
}
```

执行结果：

``` cpp
1 2 3
1 3 2
2 1 3
2 3 1
3 1 2
3 2 1
```