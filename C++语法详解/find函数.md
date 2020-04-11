---
title: find函数
date: 2019-02-07 15:37:24
categories: C++语法详解
---
&emsp;&emsp;以下所讲的所有的`string`查找函数都有唯一的返回类型，那就是`size_type`，即一个无符号整数。若查找成功，返回按查找规则找到的第一个字符或子串的位置；若查找失败，返回`npos`，即`-1`，打印出来为`4294967295`(在`32`位操作系统中为`32`个`1`)。<!--more-->

``` cpp
size_type find ( const basic_string &str, size_type pos = 0 ) const noexcept;
size_type find ( const charT *s, size_type pos = 0 ) const;
size_type find ( const charT *s, size_type pos, size_type n ) const;
size_type find ( charT c, size_type pos = 0 ) const noexcept;
```

示例如下：

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    /* 测试“size_type find (charT c, size_type pos = 0) const noexcept;” */
    string st1 ( "babbabab" );
    /* 返回1，由原型可知，若省略第2个参数，则默认从位置0(即第1个字符)起开始查找 */
    cout << st1.find ( 'a' ) << endl;
    cout << st1.find ( 'a', 0 ) << endl; /* 1 */
    cout << st1.find ( 'a', 1 ) << endl; /* 1 */
    /* 返回4，在st1中，从位置2开始查找字符a，返回首次匹配的位置；若匹配失败，返回npos */
    cout << st1.find ( 'a', 2 ) << endl;
    cout << st1.rfind ( 'a', 7 ) << endl; /* 返回6，关于rfind后面讲述 */
    cout << st1.find ( 'c', 0 ) << endl; /* 返回4294967295 */
    cout << ( st1.find ( 'c', 0 ) == -1 ) << endl; /* 返回1 */
    /* 返回1，两句均输出1，原因是计算机中-1和4294967295都表示为32个1(二进制) */
    cout << ( st1.find ( 'c', 0 ) == 4294967295 ) << endl;
    /* 返回4294967295，当查找的起始位置超出字符串长度时，按查找失败处理，返回npos */
    cout << st1.find ( 'a', 100 ) << endl;

    /* 测试“size_type find (const basic_string& str, size_type pos = 0) const noexcept;” */
    string st2 ( "aabcbcabcbabcc" );
    string str1 ( "abc" );
    /* 返回6，从st2的位置2开始匹配，返回第一次成功匹配时匹配的串(abc)的首字符在st2中的位置，失败返回npos */
    cout << st2.find ( str1, 2 ) << endl;

    /* 测试“size_type find (const charT* s, size_type pos = 0) const;” */
    cout << st2.find ( "abc", 2 ) << endl; /* 返回6，同上，只不过参数不是string而是“char*” */

    /* 测试"size_type find (const charT* s, size_type pos, size_type n) const;" */
    /* 返回6，取abcdefg的前3个字符(abc)参与匹配，相当于st2.find("abc", 2) */
    cout << st2.find ( "abcdefg", 2, 3 ) << endl;
    cout << st2.find ( "abcbc", 0, 5 ) << endl; /* 返回1，相当于st2.find("abcbc", 0) */
    /* 返回4294967295，第3个参数超出第1个参数的长度时，返回npos */
    cout << st2.find ( "abcbc", 0, 6 ) << endl;
    return 0;
}
```

应用举例：

``` cpp
/* 找出字符串str中所有的“abc”(输出位置)；若未找到，输出“not find!” */
#include <iostream>
#include <string>

using namespace std;

int main() {
    string str ( "babccbabcaabcccbabccabcabcabbabcc" );
    int num = 0;
    size_t fi = str.find ( "abc", 0 );

    while ( fi != str.npos ) {
        cout << fi << "   ";
        num++;
        fi = str.find ( "abc", fi + 1 );
    }

    if ( 0 == num ) {
        cout << "not find!";
    }

    cout << endl;
    return 0;
}
```

执行结果：

``` cpp
1   6   10   16   20   23   29
```

&emsp;&emsp;`rfind`函数原型如下：

``` cpp
size_type rfind ( const basic_string &str, size_type pos = npos ) const noexcept;
size_type rfind ( const charT *s, size_type pos = npos ) const;
size_type rfind ( const charT *s, size_type pos, size_type n ) const;
size_type rfind ( charT c, size_type pos = npos ) const noexcept;
```

`rfind`与`find`很相似，差别在于查找顺序不一样。`rfind`是从指定位置起向前查找，直到串首。上例中的`st1.rfind('a', 7)`就是从`st1`的位置`7`(`st1`的最后一个字符`b`)开始查找字符`a`，第一次找到的是倒数第`2`个字符`a`，所以返回`6`。
&emsp;&emsp;`find_first_of`函数原型如下：

``` cpp
size_type find_first_of ( const basic_string &str, size_type pos = 0 ) const noexcept;
size_type find_first_of ( const charT *s, size_type pos = 0 ) const;
size_type find_first_of ( const charT *s, size_type pos, size_type n ) const;
size_type find_first_of ( charT c, size_type pos = 0 ) const noexcept;
```

在源串中从位置`pos`起往后查找，只要在源串中遇到一个字符，该字符与目标串中任意一个字符相同，就停止查找，返回该字符在源串中的位置；若匹配失败，返回`npos`。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    /* 测试“size_type find_first_of (charT c, size_type pos = 0) const noexcept;” */
    string str ( "babccbabcc" );
    cout << str.find ( 'a', 0 ) << endl; /* 返回1 */
    /* 返回1，str.find_first_of('a', 0)与str.find('a', 0)相同 */
    cout << str.find_first_of ( 'a', 0 ) << endl;
    /* 测试“size_type find_first_of (const basic_string& str, size_type pos = 0) const noexcept;” */
    string str1 ( "bcgjhikl" );
    string str2 ( "kghlj" );
     /* 返回2，从str1的第0个字符b开始找，b不与str2中的任意字符匹配；再找c，c不与str2中的任意字符匹配；
        再找g，g与str2中的g匹配，于是停止查找，返回g在str1中的位置2 */
    cout << str1.find_first_of ( str2, 0 ) << endl;
    /* 测试“size_type find_first_of (const charT* s, size_type pos, size_type n) const;” */
    /* 返回2，尽管第3个参数超出了kghlj的长度，但仍能得到正确的结果，可以认为，str1是和“kghlj+乱码”做匹配 */
    cout << str1.find_first_of ( "kghlj", 0, 20 );
    return 0;
}
```

应用举例：

``` cpp
/* 将字符串中所有的元音字母换成“*” */
#include <iostream>
#include <string>

using namespace std;

int main() {
    std::string str ( "PLease, replace the vowels in this sentence by asterisks." );
    std::string::size_type found = str.find_first_of ( "aeiou" );

    while ( found != std::string::npos ) {
        str[found] = '*';
        found = str.find_first_of ( "aeiou", found + 1 );
    }

    std::cout << str << '\n';
    return 0;
}
```

执行结果：

``` cpp
PL**s* r*pl*c* th* v*w*ls *n th*s s*nt*nc* by *st*r*sks
```

&emsp;&emsp;`find_last_of`函数原型如下：

``` cpp
size_type find_last_of ( const basic_string &str, size_type pos = npos ) const noexcept;
size_type find_last_of ( const charT *s, size_type pos = npos ) const;
size_type find_last_of ( const charT *s, size_type pos, size_type n ) const;
size_type find_last_of ( charT c, size_type pos = npos ) const noexcept;
```

该函数与`find_first_of`函数相似，只不过查找顺序是从指定位置向前。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    /* 测试“size_type find_last_of (const charT* s, size_type pos = npos) const;” */
    /* 目标串中仅有字符c与源串中的两个c匹配，其余字符均不匹配 */
    string str ( "abcdecg" );
    /* 返回5，从str的位置6(g)开始想前找，g不匹配，再找c；c匹配，停止查找，返回c在str中的位置5 */
    cout << str.find_last_of ( "hjlywkcipn", 6 ) << endl;
    /* 返回2，从str的位置4(e)开始想前找，e不匹配，再找d；d不匹配，再找c；c匹配，停止查找，返回c在str中的位置5 */
    cout << str.find_last_of ( "hjlywkcipn", 4 ) << endl;
    /* 返回5，当第2个参数超出源串的长度(这里，str长度是7)时，不会出错，相当于从源串的最后一个字符起开始查找 */
    cout << str.find_last_of ( "hjlywkcipn", 200 ) << endl;
    return 0;
}
```

&emsp;&emsp;`find_first_not_of`函数原型：

``` cpp
size_type find_first_not_of ( const basic_string &str, size_type pos = 0 ) const noexcept;
size_type find_first_not_of ( const charT *s, size_type pos = 0 ) const;
size_type find_first_not_of ( const charT *s, size_type pos, size_type n ) const;
size_type find_first_not_of ( charT c, size_type pos = 0 ) const noexcept;
```

在源串中从位置`pos`开始往后查找，只要在源串遇到一个字符，该字符与目标串中的任意一个字符都不相同，就停止查找，返回该字符在源串中的位置；若遍历完整个源串，都找不到满足条件的字符，则返回`npos`。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    /* 测试“size_type find_first_not_of (const charT* s, size_type pos = 0) const;” */
    string str ( "abcdefg" );
    /* 返回3，从源串str的位置0(a)开始查找，目标串中有a(匹配)，再找b；b匹配，再找c；
       c匹配，再找d，目标串中没有d(不匹配)，停止查找，返回d在str中的位置3 */
    cout << str.find_first_not_of ( "kiajbvehfgmlc", 0 ) << endl;
    return 0;
}
```

&emsp;&emsp;`find_last_not_of`的函数原型如下：

``` cpp
size_type find_last_not_of ( const basic_string &str, size_type pos = npos ) const noexcept;
size_type find_last_not_of ( const charT *s, size_type pos = npos ) const;
size_type find_last_not_of ( const charT *s, size_type pos, size_type n ) const;
size_type find_last_not_of ( charT c, size_type pos = npos ) const noexcept;
```

`find_last_not_of`与`find_first_not_of`相似，只不过查找顺序是从指定位置向前的。