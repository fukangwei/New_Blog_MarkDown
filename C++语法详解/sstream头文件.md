---
title: sstream头文件
categories: C++语法详解
date: 2019-02-05 21:27:27
---
### istringstream

&emsp;&emsp;用于从流中提取数据，并且支持`>>`操作：<!--more-->

``` cpp
istringstream::istringstream ( string str );
```

&emsp;&emsp;把字符串类型的数据转换为其他类型：

``` cpp
#include <iostream>
#include <sstream>

using namespace std;

int main() {
    istringstream istr ( "1 56.7" );
    cout << istr.str() << endl; /* 输出字符串“1 56.7” */
    string str = istr.str();
    cout << str << endl;
    int n;
    double d;
    /* 以空格为间隔，把istringstream中的数据取出 */
    istr >> n;
    istr >> d;
    cout << n << endl; /* 第一个数为整型数据，输出1 */
    cout << d << endl; /* 第二个数位浮点数，输出56.7 */
    return 0;
}
```

### ostringstream

&emsp;&emsp;用于向流中写入数据，并且支持`<<`操作：

``` cpp
ostringstream::ostringstream ( string str );
```

&emsp;&emsp;代码实例：

``` cpp
#include <iostream>
#include <sstream>

using namespace std;

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

&emsp;&emsp;它是`istringstream`和`ostringstream`类的综合，支持`<<`和`>>`操作符，可以进行字符串到其它类型的快速转换。

``` cpp
stringstream::stringstream ( string str );
```

&emsp;&emsp;基本数据类型变字符串：

``` cpp
#include <fstream>
#include <iostream>
#include <sstream>

using namespace std;

int main() {
    /* int变string */
    int n = 10;
    string str;
    stringstream stream;
    stream << n;
    stream >> str;
    cout << str << endl;
    stream.clear(); /* 多次使用stringstream，要进行清空操作 */
    /* “char *”变string */
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

using namespace std;

int main() {
    /* string变double */
    double n;
    string str = "12.5";
    stringstream stream;
    stream << str;
    stream >> n;
    cout << n << endl;
    stream.clear();
    /* string变“char *” */
    string str1 = "little";
    char cStr[10];
    stream << str1;
    stream >> cStr;
    cout << cStr << endl;
    return 0;
}
```