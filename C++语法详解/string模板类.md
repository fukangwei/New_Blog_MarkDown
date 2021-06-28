---
title: string模板类
categories: C++语法详解
date: 2019-02-07 11:41:33
---
### 常用操作

&emsp;&emsp;`string`提供了一系列针对字符串的操作：<!--more-->

- `append`：在字符串的末尾添加字符。
- `find`：在字符串中查找子串。
- `length`：返回字符串的长度。
- `replace`：替换字符串。
- `substr`：返回某个子串。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string http = "www.runoob.com";
    cout << "length is " << http.length() << endl;
    http.append ( "/C++" );
    cout << "http is " << http << endl;
    int pos = http.find ( "/C++" ); /* 查找“C++”在字符串中的位置 */
    cout << "pos is " << pos << endl;
    http.replace ( pos, 4, "" ); /* 从位置pos开始，之后的4个字符被替换为空，即删除 */
    cout << "http is " << http << endl;
    int first = http.find_first_of ( "." ); /* 从头开始寻找字符“.”的位置 */
    int last = http.find_last_of ( "." ); /* 从尾开始寻找字符“.”的位置 */
    cout << "substr is " << http.substr ( first + 1, last - first - 1 ) << endl;
    return 0;
}
```

执行结果：

``` cpp
length is 14
http is www.runoob.com/C++
pos is 14
http is www.runoob.com
substr is runoob
```

### 字符串中的字符

&emsp;&emsp;`string`可以按照下标来访问每一个字符：

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1 = "1234567890";

    for ( int i = 0, len = s1.length(); i < len; i++ ) {
        cout << s1[i] << " ";
    }

    cout << endl;
    s1[5] = '5';
    cout << s1 << endl;
    return 0;
}
```

执行结果：

``` cpp
1 2 3 4 5 6 7 8 9 0
1234557890
```

### 字符串的拼接

&emsp;&emsp;可以使用`+`或`+=`来拼接`string`：

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1, s2, s3;
    s1 = "first";
    s2 = "second";
    s3 = s1 + s2;
    cout << s3 << endl; /* 输出“firstsecond” */
    s2 += s1;
    cout << s2 << endl; /* 输出“secondfirst” */
    s1 += "third";
    cout << s1 << endl; /* 输出“firstthird” */
    s1 += 'a';
    cout << s1 << endl; /* 输出“firstthirda” */
    return 0;
}
```