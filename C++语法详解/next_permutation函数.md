---
title: next_permutation函数
categories: C++语法详解
date: 2018-12-27 10:28:40
---
&emsp;&emsp;`STL`提供了两个用来计算排列组合关系的算法，分别是`next_permutation`和`prev_permutation`。首先了解什么是`下一个`排列组合，什么是`前一个`排列组合。<!--more-->
&emsp;&emsp;考虑三个字符所组成的序列`{a, b, c}`，这个序列有六个可能的排列组合，即`abc`、`acb`、`bac`、`bca`、`cab`和`cba`，这些排列组合根据`less-than`操作符做字典顺序(`lexicographical`)的排序。也就是说，`abc`名列第一，因为元素`a`都小于其后的元素；`acb`是次一个排列组合，因为它是固定了`a`(序列内最小元素)之后所做的新组合。同样道理，那些固定`b`(序列中次小元素)而做的排列组合，在次序上将先于那些固定`c`而做的排列组合。以`bac`和`bca`为例，`bac`在`bca`之前，因为次序`ac`小于序列`ca`。面对`bca`，我们可以说其前一个排列组合是`bac`，而其后一个排列组合是`cab`。序列`abc`没有`前一个`排列组合，`cba`没有`后一个`排列组合。
&emsp;&emsp;`next_permutation`会取得`[first, last)`所标示之序列的下一个排列组合，如果没有下一个排列组合，便返回`false`，否则返回`true`：

``` cpp
#include <iostream>
#include <algorithm>
#include "stdlib.h"

using namespace std;

int main() {
    int ans[4] = { 1, 2, 3, 4 };
    sort(ans, ans + 4); /* 这个sort可以不用，因为{1, 2, 3, 4}已经排好序*/

    do { /* 注意这一步，如果是while循环，则需要提前输出 */
        for (int i = 0; i < 4; ++i) {
            cout << ans[i] << " ";
        }

        cout << endl;
    } while (next_permutation(ans, ans + 4));

    system("pause");
    return 0;
}
```