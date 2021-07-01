---
title: string模板类
categories: C++语法详解
date: 2019-02-07 11:41:33
---
### 定义string变量

&emsp;&emsp;`C++`大大增强了对字符串的支持，除了可以使用`C`风格的字符串，还可以使用内置的`string`类。
&emsp;&emsp;与`C`风格的字符串不同，`string`的结尾没有结束标志`NUL`。
&emsp;&emsp;下面的例子介绍了几种定义`string`变量的方法：

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1;
    string s2 = "c plus plus";
    string s3 = s2;
    string s4 ( 5, 's' );
    return 0;
}
```

- `s1`只是定义但没有初始化，编译器会将空字符串赋给`s1`。
- `s2`在定义的同时，被初始化为`c plus plus`。
- `s3`在定义的时候，直接用`s2`进行初始化，因此`s3`的内容也是`c plus plus`。
- `s4`被初始化为由`5`个字符`s`组成的字符串，也就是`sssss`。

### string的长度

&emsp;&emsp;当需要知道字符串的长度时，可以调用`string`类提供的`length`函数：

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s = "http://c.biancheng.net";
    int len = s.length();
    cout << len << endl; /* 输出22 */
    return 0;
}
```

### string的输入输出

&emsp;&emsp;`string`类重载了输入输出运算符，因此可以像对待普通变量那样对待`string`变量，也就是用`>>`进行输入，用`<<`进行输出：

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s;
    cin >> s; /* 输入字符串 */
    cout << s << endl; /* 输出字符串 */
    return 0;
}
```

执行结果：

``` cpp
http://c.biancheng.net http://vip.biancheng.net
http://c.biancheng.net
```

&emsp;&emsp;虽然输入了两个由空格隔开的网址，但是只输出了一个，这是因为`>>`默认会忽略空格，遇到空格就认为输入结束。

### 访问字符串中的字符

&emsp;&emsp;`string`也可以像`C`风格的字符串一样，按照下标来访问其中的每一个字符。
&emsp;&emsp;`string`的起始下标仍是从`0`开始：

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s = "1234567890";

    for ( int i = 0, len = s.length(); i < len; i++ ) {
        cout << s[i] << " ";
    }

    cout << endl;
    s[5] = '5';
    cout << s << endl;
    return 0;
}
```

运行结果：

``` cpp
1 2 3 4 5 6 7 8 9 0
1234557890
```

### 字符串的拼接

&emsp;&emsp;`string`可以使用`+`或`+=`来直接拼接字符串：

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1 = "first ";
    string s2 = "second";
    char* s3 = ( char * ) "third";
    char s4[] = "fourth";
    char ch = '@';
    string s5 = s1 + s2;
    string s6 = s1 + s3;
    string s7 = s1 + s4;
    string s8 = s1 + ch;
    cout << s5 << endl << s6 << endl << s7 << endl << s8;
    return 0;
}
```

执行结果：

``` cpp
first second
first third
first fourth
first @
```

### 插入字符串

&emsp;&emsp;`insert`可以在`string`的指定位置插入另一个字符串：

``` cpp
string& insert ( size_t pos, const string& str );
```

- `pos`表示要插入的位置，也就是下标。
- `str`表示要插入的字符串，它可以是`string`字符串，也可以是`C`风格的字符串。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1, s2, s3;
    s1 = s2 = "1234567890";
    s3 = "aaa";
    s1.insert ( 5, s3 );
    cout << s1 << endl;
    s2.insert ( 5, "bbb" );
    cout << s2 << endl;
    return 0;
}
```

执行结果：

``` cpp
12345aaa67890
12345bbb67890
```

### 删除字符串

&emsp;&emsp;`erase`函数可以删除`string`中的一个子字符串：

``` cpp
string& erase ( size_t pos = 0, size_t len = npos );
```

- `pos`表示要删除的子字符串的起始下标。
- `len`表示要删除子字符串的长度。如果不指明`len`，那么直接删除从`pos`到字符串结束处的所有字符。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1, s2, s3;
    s1 = s2 = s3 = "1234567890";
    s2.erase ( 5 );
    s3.erase ( 5, 3 );
    cout << s1 << endl;
    cout << s2 << endl;
    cout << s3 << endl;
    return 0;
}
```

执行结果：

``` cpp
1234567890
12345
1234590
```

### 提取子字符串

&emsp;&emsp;`substr`用于从`string`中提取子字符串：

``` cpp
string substr ( size_t pos = 0, size_t len = npos ) const;
```

- `pos`是要提取的子字符串的起始下标。
- `len`是要提取的子字符串的长度。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1 = "first second third";
    string s2;
    s2 = s1.substr ( 6, 6 );
    cout << s1 << endl;
    cout << s2 << endl;
    return 0;
}
```

执行结果：

``` cpp
first second third
second
```

&emsp;&emsp;系统对`substr`参数的处理和`erase`类似：

- 如果`pos`越界，会抛出异常。
- 如果`len`越界，会提取从`pos`到字符串结尾处的所有字符。

### 字符串查找

&emsp;&emsp;`string`类提供了几个与字符串查找有关的函数。

#### find

&emsp;&emsp;`find`函数用于在`string`中查找子字符串出现的位置：

``` cpp
size_t find ( const string& str, size_t pos = 0 ) const;
size_t find ( const char* s, size_t pos = 0 ) const;
```

- 第一个参数为待查找的子字符串，它可以是`string`字符串，也可以是`C`风格的字符串。
- 第二个参数为开始查找的位置，如果不指明，则从第`0`个字符开始查找。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1 = "first second third";
    string s2 = "second";
    int index = s1.find ( s2, 5 );

    if ( index < s1.length() ) {
        cout << "Found at index: " << index << endl;
    } else {
        cout << "Not found" << endl;
    }

    return 0;
}
```

执行结果：

``` cpp
Found at index: 6
```

#### rfind

&emsp;&emsp;`rfind`和`find`很类似，同样是在字符串中查找子字符串。不同的是：

- `find`函数从第二个参数开始往后查找。
- `rfind`函数则最多查找到第二个参数处。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1 = "first second third";
    string s2 = "second";
    int index = s1.rfind ( s2, 6 );

    if ( index < s1.length() ) {
        cout << "Found at index: " << index << endl;
    } else {
        cout << "Not found" << endl;
    }

    return 0;
}
```

执行结果：

``` cpp
Found at index: 6
```

#### find_first_of

&emsp;&emsp;`find_first_of`函数用于查找子字符串和字符串共同具有的字符在字符串中首次出现的位置：

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1 = "first second second third";
    string s2 = "asecond";
    int index = s1.find_first_of ( s2 );

    if ( index < s1.length() ) {
        cout << "Found at index: " << index << endl;
    } else {
        cout << "Not found" << endl;
    }

    return 0;
}
```

执行结果：

``` cpp
Found at index : 3
```