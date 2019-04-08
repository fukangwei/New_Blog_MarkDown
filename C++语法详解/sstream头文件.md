---
title: sstream头文件
date: 2019-02-05 21:27:27
tags:
---
&emsp;&emsp;`C++`的输入输出分为三种：
&emsp;&emsp;1. 基于控制台的`I/O`，头文件是`iostream`：

- `istream`：从流中读取。
- `ostream`：写到流中去。
- `iostream`：对流进行读写，从`istream`和`ostream`派生。

&emsp;&emsp;2. 基于文件的`I/O`，头文件是`fstream`：

- `ifstream`：从文件中读取，由`istream`派生。
- `ofstream`：写到文件中去，由`ostream`派生。
- `fstream`：对文件进行读写，由`iostream`派生。

&emsp;&emsp;3. 基于字符串的`I/O`，头文件是`sstream`：

- `istringstream`：从`string`对象中读取，由`istream`派生，用于执行`C++`风格的字符串流的输入操作。
- `ostringstream`：写到`string`对象中去，由`ostream`派生，用于执行`C++`风格的字符串流的输出操作。
- `strstream`：对`string`对象进行读写，由`iostream`派生，同时可以支持`C++`风格的串流的输入输出操作。

### istringstream

&emsp;&emsp;从流中提取数据，支持`>>`操作。这里的字符串可以包括多个单词，单词之间使用空格分开。`istringstream`的构造函数原型：

``` cpp
istringstream::istringstream ( string str );
```

使用字符串进行初始化：

``` cpp
istringstream istr ( "1 56.7" );
istr.str ( "1 56.7" ); /* 把字符串“1 56.7”存入字符串流中 */
```

常用成员函数：

``` cpp
str(); /* 使istringstream对象返回一个string字符串 */
```

&emsp;&emsp;把字符串类型的数据转换为其他类型：

``` cpp
#include <iostream>
#include <sstream>
​
using namespace std;
​
int main() {
    istringstream istr ( "1 56.7" );
    cout << istr.str() << endl; /* 直接输出字符串“1 56.7” */
    string str = istr.str(); /* 函数str返回一个字符串 */
    cout << str << endl;
    int n;
    double d;
    /* 以空格为界，把istringstream中数据取出，应进行类型转换 */
    istr >> n; /* 第一个数为整型数据，输出1 */
    istr >> d; /* 第二个数位浮点数，输出56.7 */
    cout << n << endl;
    cout << d << endl;
    return 0;
}
```

&emsp;&emsp;把一行字符串放入流中，单词以空格隔开，再把单词从流中依次读取到字符串：

``` cpp
#include <iostream>
#include <sstream>
​
using namespace std;
​
int main() {
    istringstream istr;
    string line, str;
​
    while ( getline ( cin, line ) ) { /* 从终端接收一行字符串，并放入字符串line中 */
        istr.str ( line ); /* 把line中的字符串存入字符串流中 */
​
        while ( istr >> str ) { /* 每次读取一个单词(以空格为界)，存入str中 */
            cout << str << endl;
        }
    }
​
    return 0;
}
```

### ostringstream

&emsp;&emsp;把其他类型的数据写入流(往流中写入数据)，支持`<<`操作。`ostringstream`的构造函数原型：

``` cpp
ostringstream::ostringstream ( string str );
```

使用字符串进行初始化：

``` cpp
ostringstream ostr ( "1234" );
ostr.str ( "1234" ); /* 把字符串“1234”存入字符串流中 */
```

示例如下：

``` cpp
#include <iostream>
#include <sstream>
​
using namespace std;
​
int main() {
    ostringstream ostr ( "1234" ); /* 初始化输出字符串流ostr */
    cout << ostr.str() << endl; /* 输出“1234” */
    ostr.put ( '5' ); /* 字符“5”顶替了“1”的位置 */
    cout << ostr.str() << endl; /* 输出“5234” */
    ostr << "67"; /* 字符串“67”替代了“23”的位置，输出5674 */
    string str = ostr.str();
    cout << str << endl;
    return 0;
}
```

### stringstream

&emsp;&emsp;它是对`istringstream`和`ostringstream`类的综合，支持`<<`和`>>`操作符，可以进行字符串到其它类型的快速转换。`stringstream`的构造函数原型：

``` cpp
stringstream::stringstream ( string str );
```

使用字符串进行初始化：

``` cpp
stringstream str ( "1234" );
str.str ( "1234" ); /* 把字符串“1234”存入字符串流中 */
```

其作用主要有两个：数据转换或者将文件的所有数据一次性读入内存。
&emsp;&emsp;基本数据类型变字符串：

``` cpp
#include <fstream>
#include <iostream>
#include <sstream>
​
using namespace std;
​
int main() {
    /* int变string */
    int n = 10;
    string str;
    stringstream stream;
    stream << n;
    stream >> str; /* 注意，不要使用“stream.str()” */
    cout << str << endl;
    /* 多次使用stringstream，要进行清空操作，不能使用“stream.str("");”否则下面输出10 */
    stream.clear();
    /* “char*”变string */
    char cStr[10] = "little";
    stream << cStr;
    stream >> str;
    cout << str << endl;
    return 0;
}
```

&emsp;&emsp;字符串变基本数据类型：

``` cpp
#include <fstream>
#include <iostream>
#include <sstream>
​
using namespace std;
​
int main() {
    /* string变double */
    double n;
    string str = "12.5";
    stringstream stream;
    stream << str;
    stream >> n;
    cout << n << endl;
    stream.clear();
    /*string变char* */
    string str1 = "little";
    char cStr[10];
    stream << str1;
    stream >> cStr;
    cout << cStr << endl;
    return 0;
}
```

注意如下代码：

``` cpp
#include <iostream>
#include <sstream>
​
using namespace std;
​
int main ( int argc, char *argv[] ) {
    std::stringstream stream;
    string str;
​
    while ( 1 ) {
        /* clear这个名字让很多人想当然地认为它会清除流的内容。实际上
           它并不清空任何内容，只是重置了流的状态标志而已 */
        stream.clear();
        /* 去掉下面这行注释，清空stringstream的缓冲。每次循环后，内存消耗将不再增加 */
        // stream.str("");
        stream << "sdfsdfdsfsadfsdafsdfsdgsdgsdgsadgdsgsdagasdgsdagsadgsdgsgdsagsadgs";
        stream >> str;
        cout << "Size of stream = " << stream.str().length() << endl;
        system ( "PAUSE" );
    }
​
    system ( "PAUSE" );
    return 0;
}
```