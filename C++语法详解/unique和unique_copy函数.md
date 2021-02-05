---
title: unique和unique_copy函数
categories: C++语法详解
date: 2019-02-05 20:34:59
---
&emsp;&emsp;`unique`函数的功能是去除相邻的重复元素(只保留一个)：<!--more-->

``` cpp
unique(first, last, compare);
```

其中`first`为容器的首迭代器，`last`为容器的末迭代器，`compare`为比较函数(可省略)。注意，`unique`函数也并非真正地删除了元素，一般要与`erase`成员函数或`resize`成员函数互相配合使用。
&emsp;&emsp;删除字符串中相邻的重复元素并打印字符串：

``` cpp
#include <iostream>
#include <algorithm>
#include <string>

using namespace std;

int main ( void ) {
    string str;
    cin >> str;
    str.erase ( unique ( str.begin(), str.end() ), str.end() );
    // str.resize ( unique ( str.begin(), str.end() ) - str.begin() );
    cout << str << endl;
    return 0;
}
```

输入`abbbccbba`，输出`abcba`。
&emsp;&emsp;`unique_copy`与`unique`的唯一区别在于：`unique_copy`会将进行去除相邻重复元素的结果保存在另外一个结果容器中：

``` cpp
unique_copy(first, last, result, compare);
```

其中`first`为容器的首迭代器，`last`为容器的末迭代器，`result`为保存结果的容器(原容器的内容不变)，`compare`为比较函数(可省略)。实现上面同样功能的代码如下：

``` cpp
#include <iostream>
#include <algorithm>
#include <string>

using namespace std;

int main ( void ) {
    string first, second;
    cin >> first;
    second.resize ( first.size() );
    second.resize ( unique_copy ( first.begin(), first.end(), second.begin() ) - second.begin() );
    cout << second << endl;
    return 0;
}
```


---

&emsp;&emsp;`unique`函数是将重复的元素折叠缩编，使成唯一；`unqiue_copy`函数是将重复的元素折叠缩编，使成唯一，并复制到其他地方。这两个函数的剔除字符原理是：看当前字符与它前一个字符是否相同，如果相同就剔除当前字符；如果不同就跳转到下一个字符。所以，在求一个字符串的字符集的时候，要先把字符串排个序再调用上面两个函数剔除重复字符，获取字符集。

``` cpp
string str = "zhaohaoyang";
/* 根据迭代器的起始位置和终止位置来定初始化一个容器 */
vector<char> vecch ( str.begin(), str.end() );
vector<char>::iterator it = vecch.begin();

for ( ; it != vecch.end(); ++it ) {
    cout << *it;
}

cout << endl; /* 输出“zhaohaoyang” */
unique ( str.begin(), str.end() );
out << str << endl; /* 输出的还是“zhaohaoyang” */
```

`unique`是剔除重复的元素没错，但它是剔除相邻之间重复的元素。`str`中的字符前后之间是没有重复的，所以`unique`函数是起不到作用。假如把`str`换成`acttacct`，则执行结果是`actactct`，这个结果也是奇怪的。按照意愿应该是把多余的`t`与多余的`c`剔掉后只剩下`actact`，最后两个字符`ct`是怎么回事呢？因为`unique`函数并不是真的在源字符串上进行剔除，原来的字符串经过`unique`函数后虽然除掉了相邻之间重复的字符，可是字符串长度是不变的，也就是说所占内存大小没变。遇到这个问题通常会使用语句`str.erase(unique(str.begin(), str.end()), str.end());`。
&emsp;&emsp;`unique`函数的返回值是源字符串中去除相邻之间相同字符后剩下的字符串中的最后一个字符的下一个位置，例如`acttacct`有`8`个字符，相邻之间重复的有两个。`acttacct`被`unique`后的字符串面貌是`actactXX`，`X`代表的是不确定的字符。`unique`函数的返回值是一个迭代器类型，指向的是第一个`X`所处的位置。`STL`中的参数区间都是左闭右开的，`str.end`返回的迭代器指向第二个`X`位置的后面一个位置，所以调用`str.erase`后就把后两个不确定的字符`XX`删除掉了。最后得到的就是我们最开始想要的结果了，即`actact`。
&emsp;&emsp;前面说到，`unique`和`unique_copy`函数都是针对相邻之间相同字符的剔除，如果不用`sort`函数，那么我们想要获取一个字符串所包含的的字符集并不是每一次都能保证正确的(如果字符串中没有相同的字符就是正确的，如果相同的字符都是在相邻位置出现结果也是正确的，但大多数情况下却不是这么回事)。使用`sort`后的代码如下：

``` cpp
string str = "zhaohaoyang";
vector<char> vecch ( str.begin(), str.end() );
sort ( str.begin(), str.end() );
str.erase ( unique ( str.begin(), str.end() ), str.end() );
cout << str << endl; /* 输出“aghnoyz” */
```

现在可以从一个字符串中剔除重复的字符，获取字符串的字符集了。关键在于使用`sort`函数对字符串进行排序，排序后的字符串相同字符都会集中出现，所以`unique`就能正确发挥功能了。
&emsp;&emsp;上面所展示的是把获取的字符集存储在了源字符串变量中，如果遇到要把字符集放置在额外的变量中的情况，就要用到`unique_copy`函数了。来看一段代码：

``` cpp
string str = "zhaohaoyang";
string dststr;
dststr.resize ( str.size() );
sort ( str.begin(), str.end() );
dststr.resize ( unique_copy ( str.begin(), str.end(), dststr.begin() ) - dststr.begin() );
cout << dststr << endl; /* 输出“aghnoyz” */
```

`dststr`是存储字符集的目标串，`str`是源字符串变量。`dststr`在用之前必须要先设置大小，否则到`unique_copy`的时候会报内存错误。因为是要存储字符串的字符集，所以`dststr`设置和源字符串相同的大小是完全可以的，最后再把剩余的位置`erase`掉就可以了。