---
title: Stack
date: 2021-02-24 20:35:44
categories: C++语法详解
---
### 语法

&emsp;&emsp;`Stack`的语法如下：<!--more-->

``` cpp
template<class T, class Container = deque<T> > class stack;
```

- `T`：指定容器适配器将保留的元素的类型。
- `Container`：指定容器的内部对象，用于容纳堆栈的元素。

这种数据结构使用`LIFO`技术，即后进先出，最先插入的元素将在末尾提取。有一个名为`top`的元素，它是位于最上面位置的元素。所有插入和删除操作都是在堆栈的顶部元素本身进行的。

### 函数

&emsp;&emsp;堆栈提供了如下的接口函数：

函数     | 描述
--------|-----
`empty` | 该函数用于测试堆栈是否为空。如果堆栈为空，则该函数返回`true`，否则返回`false`
`size`  | 该函数返回堆栈容器的大小，该大小是堆栈中存储的元素数量的度量
`top`   | 该函数用于访问堆栈的顶部元素
`push`  | 该函数用于在堆栈顶部插入新元素
`pop`   | 该函数用于删除元素，堆栈中的元素将从顶部开始删除

### 使用方法

&emsp;&emsp;使用方法如下：

``` cpp
#include <iostream>
#include <stack>

using namespace std;

void newstack ( stack <int> ss ) {
    stack <int> sg = ss;

    while ( !sg.empty() ) {
        cout << sg.top() << " ";
        sg.pop();
    }
}

int main () {
    stack <int> newst;
    newst.push ( 55 );
    newst.push ( 44 );
    newst.push ( 33 );
    newst.push ( 22 );
    newst.push ( 11 );

    cout << "最新的堆栈是：";
    newstack ( newst );
    cout << "\nnewst.size()：" << newst.size();
    cout << "\nnewst.top()：" << newst.top();
    cout << "\nnewst.pop()：";
    newst.pop();
    newstack ( newst );
    cout << '\n';
    return 0;
}
```

执行结果：

``` cpp
最新的堆栈是：11 22 33 44 55 
newst.size()：5
newst.top()：11
newst.pop()：22 33 44 55
```