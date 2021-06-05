---
title: istream_iterator和ostream_iterator
categories: C++语法详解
date: 2019-03-11 12:10:03
---
&emsp;&emsp;流迭代器也是一种迭代器，它的操作对象不再是某个容器，而是流对象。通过流迭代器，我们可以读取指定流对象中的数据，也可以将数据写入到流对象中。

### istream_iterator

&emsp;&emsp;`istream_iterator`用于直接从指定的输入流中读取元素，它只能进行`++p`、`p++`、`*p`操作。`istream_iterator`之间也只能使用`==`和`!=`运算符。
&emsp;&emsp;创建`istream_iterator`的方式有如下`3`种：
&emsp;&emsp;1. 调用`istream_iterator`的默认构造函数，可以创建出一个具有结束标志的`istream_iterator`：

``` cpp
std::istream_iterator<double> eos; /* 可读取double类型元素，并代表结束标志 */
```

&emsp;&emsp;2. 创建一个可用来读取数据的`istream_iterator`：

``` cpp
std::istream_iterator<double> iit ( std::cin ); /* 可从标准输入流cin读取数据 */
```

&emsp;&emsp;3. `istream_iterator`还支持用已创建好的`istream_iterator`为新建的`istream_iterator`初始化：

``` cpp
std::istream_iterator<double> iit2 ( iit1 ); /* 和iit1完全相同的输入流迭代器 */
```

&emsp;&emsp;代码如下：

``` cpp
#include <iostream>
#include <iterator>

using namespace std;

int main() {
    double value1, value2; /* 用于接收输入流中的数据 */
    cout << "请输入2个小数：";
    istream_iterator<double> eos; /* 创建表示结束的输入流迭代器 */
    istream_iterator<double> iit ( cin ); /* 创建一个可逐个读取输入流中数据的迭代器 */

    /* 只有读取到EOF流结束符时，iit才会和eos相等 */
    if ( iit != eos ) { /* 判断输入流中是否有数据 */
        value1 = *iit; /* 读取一个元素，并赋值给value1 */
    }

    iit++; /* iit迭代器后移一位，准备读取下一个元素 */

    if ( iit != eos ) {
        value2 = *iit; /* 读取第二个元素，赋值给value2 */
    }

    cout << "value1 = " << value1 << endl;
    cout << "value2 = " << value2 << endl;
    return 0;
}
```

执行结果：

``` bash
请输入2个小数：12.4 13.5
value1 = 12.4
value2 = 13.5
```

### ostream_iterator

&emsp;&emsp;`ostream_iterator`用于将数据写到指定的输出流中，它能执行`++p`、`p++`、`*p = t`以及`*p++ = t`等类似操作。
&emsp;&emsp;创建`ostream_iterator`的方式有如下`3`种：
&emsp;&emsp;1. 调用`ostream_iterator`的默认构造函数，可以创建了一个指定输出流的`istream_iterator`：

``` cpp
/* 可将int类型元素写入到标准输出中 */
std::ostream_iterator<int> out_it ( std::cout );
```

&emsp;&emsp;2. 在第一种方式的基础上，还可以为写入的元素之间指定一个分隔符：

``` cpp
/* 在向标准输出写入int类型元素的同时，还会附带写入一个逗号 */
std::ostream_iterator<int> out_it ( std::cout，"," );
```

&emsp;&emsp;3. `ostream_iterator`还支持用已创建好的`ostream_iterator`为新建的`ostream_iterator`初始化：

``` cpp
/* 利用已创建的out_it再创建一个完全相同的out_it1 */
std::ostream_iterator<int> out_it1 ( out_it );
```

&emsp;&emsp;代码如下：

``` cpp
#include <iostream>
#include <iterator>
#include <string>

using namespace std;

int main() {
    ostream_iterator<string> out_it ( cout ); /* 创建一个输出流迭代器 */

    *out_it = "http://c.biancheng.net/stl/"; /* 向cout写入string字符串 */
    cout << endl;
    ostream_iterator<int> out_it1 ( cout, "," ); /* 创建一个输出流迭代器，设置分隔符 */
    /* 向cout依次写入1、2、3 */
    *out_it1 = 1;
    *out_it1 = 2;
    *out_it1 = 3;
    cout << endl;
    return 0;
}
```

执行结果：

``` bash
http://c.biancheng.net/stl/
1,2,3,
```

### 与copy函数连用

&emsp;&emsp;在实际场景中，`ostream_iterator`常和`copy`函数连用：

``` cpp
#include <iostream>
#include <iterator>
#include <vector>
#include <algorithm>

using namespace std;

int main() {
    vector<int> myvector; /* 创建一个vector容器 */

    for ( int i = 1; i < 10; ++i ) {
        myvector.push_back ( i );
    }

    std::ostream_iterator<int> out_it ( std::cout, ", " );
    /* 将myvector中存储的元素写入到cout中 */
    std::copy ( myvector.begin(), myvector.end(), out_it );
    cout << endl;
    return 0;
}
```

执行结果：

``` bash
1, 2, 3, 4, 5, 6, 7, 8, 9,
```