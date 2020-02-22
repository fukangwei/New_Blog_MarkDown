---
title: queue模板类
categories: C++语法详解
date: 2019-02-05 21:21:00
---
&emsp;&emsp;`C++`队列`queue`模板类的定义在`queue.h`。`queue`模板类需要两个模板参数，即元素类型和容器类型，元素类型是必要的，容器类型是可选的，默认为`deque`类型。<!--more-->
&emsp;&emsp;队列`Queue`是一种容器适配器，它给予程序员一种先进先出(`FIFO`)的数据结构，其成员函数如下：

- `back`：返回最后一个元素。
- `empty`：如果队列为空，则返回真。
- `front`：返回第一个元素。
- `pop`：删除第一个元素。
- `push`：在末尾加入一个元素。
- `size`：返回队列中元素的个数。

&emsp;&emsp;定义`queue`对象的示例代码如下：

``` cpp
queue<int> q1;
queue<double> q2;
```

&emsp;&emsp;`queue`的基本操作如下：

- `queue`入队：`q.push(x);`将`x`连接到队列的末端。
- `queue`出队：`q.pop`弹出队列的第一个元素，注意并不会返回被弹出元素的值。
- 访问`queue`队首元素：`q.front`即最早被压入队列的元素。
- 访问`queue`队尾元素：`q.back`即最后被压入队列的元素。
- 判断`queue`队列空：`q.empty`当队列空时，返回`true`。
- 访问队列中的元素个数：使用`q.size`。

``` cpp
#include <cstdlib>
#include <iostream>
#include <queue>

using namespace std;

int main ( void ) {
    int e, n, m;
    queue<int> q1;

    for ( int i = 0; i < 10; i++ ) {
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

    system ( "PAUSE" );
    return 0;
}
```