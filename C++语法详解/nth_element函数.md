---
title: nth_element函数
categories: C++语法详解
date: 2019-02-05 19:48:44
---
&emsp;&emsp;函数原型如下：<!--more-->

``` cpp
#include <algorithm>
void nth_element ( first, nth, last, compare );
```

求`[first, last]`这个区间中第`nth`大小的元素，如果参数加入了`compare`函数，就按照`compare`函数的方式比较。
&emsp;&emsp;`nth_element`仅排序第`nth`个元素(从`0`开始索引)，就是将位置`nth`(从`0`开始)的元素放在第`nth`大的位置，处理完之后，默认排在它前面的元素都不比它大，排在它后面的元素都不比它小。
&emsp;&emsp;例如`array[first, last)`元素区间，排序后，`array[nth]`就是第`nth`大的元素(从`0`开始)，但是`[first, nth)`和`[nth, last)`区间的大小顺序不一定。但是可以确定的是`array[nth]`一定是整个区间里第`n`大的元素。`[first, nth)`中的元素都是不大于`array[nth]`的，`[nth, last)`中的元素都是不小于`array[nth]`的。

``` cpp
#include <iostream>
#include <algorithm>

using namespace std;

int main() {
    int array[] = {5, 6, 7, 8, 4, 2, 1, 3, 0};
    int len = sizeof ( array ) / sizeof ( int );
    cout << "排序前: ";

    for ( int i = 0; i < len; i++ ) {
        cout << array[i] << " ";
    }

    nth_element ( array, array + 6, array + len );
    cout << endl;
    cout << "排序后：";

    for ( int i = 0; i < len; i++ ) {
        cout << array[i] << " ";
    }

    cout << endl << "第6个元素为" << array[6] << endl;
}
```