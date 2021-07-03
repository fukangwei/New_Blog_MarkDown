---
title: to_string函数
categories: C++语法详解
date: 2019-02-05 18:08:38
---
&emsp;&emsp;函数`to_string`用于把数字类型转换为`string`类型：<!--more-->

``` cpp
#include <iostream>
#include <string>

int main ( void ) {
    double f = 23.43;
    double f2 = 1e-9;
    double f3 = 1e40;
    double f4 = 1e-40;
    double f5 = 123456789;
    std::string f_str = std::to_string ( f );
    std::string f_str2 = std::to_string ( f2 ); /* Note: returns "0.000000" */
    std::string f_str3 = std::to_string ( f3 ); /* Note: Does not return "1e+40" */
    std::string f_str4 = std::to_string ( f4 ); /* Note: returns "0.000000" */
    std::string f_str5 = std::to_string ( f5 );
    std::cout << "std::cout: " << f << '\n'
              << "to_string: " << f_str << '\n'
              << "std::cout: " << f2 << '\n'
              << "to_string: " << f_str2 << "\n"
              << "std::cout: " << f3 << '\n'
              << "to_string: " << f_str3 << '\n'
              << "std::cout: " << f4 << '\n'
              << "to_string: " << f_str4 << '\n'
              << "std::cout: " << f5 << '\n'
              << "to_string: " << f_str5 << '\n';
}
```

执行结果：

``` cpp
std::cout: 23.43
to_string: 23.430000
std::cout: 1e-09
to_string: 0.000000
std::cout: 1e+40
to_string: 10000000000000000303786028427003666890752.000000
std::cout: 1e-40
to_string: 0.000000
std::cout: 1.23457e+08
to_string: 123456789.000000
```