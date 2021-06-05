---
title: hardware_concurrency函数
date: 2021-03-03 06:02:11
categories: C++语法详解
---
&emsp;&emsp;`hardware_concurrency`返回实现支持的并发线程数。如果该值不可计算，则返回`0`：<!--more-->

``` cpp
static unsigned int hardware_concurrency() noexcept;
```

代码如下：

``` cpp
#include <iostream>
#include <thread>

int main() {
    unsigned int n = std::thread::hardware_concurrency();
    std::cout << n << " concurrent threads are supported.\n";
}
```

执行结果：

``` bash
4 concurrent threads are supported.
```