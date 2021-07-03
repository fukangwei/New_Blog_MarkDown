---
title: queue模板类
categories: C++语法详解
date: 2019-02-05 21:21:00
---
### Queue

&emsp;&emsp;队列`Queue`提供一种先进先出的数据结构，即`FIFO`。其成员函数如下：<!--more-->

- `back`：返回最后一个元素。
- `empty`：如果队列为空，则返回`True`。
- `front`：返回第一个元素。
- `pop`：删除第一个元素。
- `push`：在末尾加入一个元素。
- `size`：返回队列中元素的个数。

### 代码实例

&emsp;&emsp;代码实例：

``` cpp
#include <cstdlib>
#include <iostream>
#include <queue>

using namespace std;

int main ( void ) {
    int e, n, m;
    queue<int> q1;

    for ( int i = 0; i < 5; i++ ) {
        q1.push ( i );
    }

    if ( !q1.empty() ) {
        cout << "the queue is not empty" << endl;
    }

    n = q1.size();
    cout << "the size of the queue is " << n << endl;
    m = q1.back();
    cout << "the last element of the queue is " << m << endl;

    for ( int j = 0; j < n; j++ ) {
        e = q1.front();
        cout << e << " ";
        q1.pop();
    }

    cout << endl;

    if ( q1.empty() ) {
        cout << "the queue is empty" << endl;
    }

    return 0;
}
```

执行结果：

``` cpp
the queue is not empty
the size of the queue is 5
the last element of the queue is 4
0 1 2 3 4
the queue is empty
```