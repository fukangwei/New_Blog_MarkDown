---
title: hardware_concurrency函数
date: 2021-03-03 06:02:11
categories: C++语法详解
---
&emsp;&emsp;`hardware_concurrency` returns the number of concurrent threads supported by the implementation. If the value is not well defined or not computable, returns `0`.<!--more-->

``` cpp
static unsigned int hardware_concurrency() noexcept;
```

Example:

``` cpp
#include <iostream>
#include <thread>

int main() {
    unsigned int n = std::thread::hardware_concurrency();
    std::cout << n << " concurrent threads are supported.\n";
}
```

Possible output:

``` bash
4 concurrent threads are supported.
```