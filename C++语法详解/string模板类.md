---
title: string模板类
categories: C++语法详解
date: 2019-02-07 11:41:33
---
### 访问字符串中的字符

&emsp;&emsp;`string`字符串也可以像字符串数组一样，按照下标来访问其中的每一个字符。`string`字符串的起始下标仍是从`0`开始。<!--more-->

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1 ;
    s1 = "1234567890";

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

本例中定义了一个`string`变量`s1`，并赋值`1234567890`，之后用`for`循环遍历输出每一个字符。借助下标，除了能够访问每个字符，也可以修改每个字符。`s1[5] = '5';`将第`6`个字符修改为`5`，所以`s1`最后为`1234557890`。

### 字符串的拼接

&emsp;&emsp;可以使用`+`或`+=`运算符来直接拼接`string`字符串，再也不需要使用`C`语言的`strcat`、`strcpy`、`malloc`等函数。
&emsp;&emsp;使用`+`拼接字符串时，运算符的两边可以都是`string`字符串，也可以是`string`字符串和`C`风格的字符串，还可以是一个`string`字符串和一个`char`字符。

``` cpp
#include <iostream>
#include <string>

using namespace std;

int main() {
    string s1, s2, s3;
    s1 = "first";
    s2 = "second";
    s3 = s1 + s2;
    cout << s3 << endl;
    s2 += s1;
    cout << s2 << endl;
    s1 += "third";
    cout << s1 << endl;
    s1 += 'a';
    cout << s1 << endl;
    return 0;
}
```

执行结果：

``` cpp
firstsecond
secondfirst
firstthird
firstthirda
```

---

### string

&emsp;&emsp;It is a public member function, in the header file `string`.

Version            | Function
-------------------|---------
`default`          | `string();`
`copy`             | `string (const string& str);`
`substring`        | `string (const string& str, size_t pos, size_t len = npos);`
`from c-string`    | `string (const char* s);`
`from buffer`      | `string (const char* s, size_t n);`
`fill`             | `string (size_t n, char c);`
`range`            | `string (InputIterator first, InputIterator last);`(`template <class InputIterator>`)
`initializer list` | `string (initializer_list<char> il);`
`move`             | `string (string&& str) noexcept;`

`size_t` is an unsigned integral type. Constructs a `string` object, initializing its value depending on the constructor version used:

- empty `string` constructor (default constructor): Constructs an empty `string`, with a length of zero characters.
- copy constructor: Constructs a copy of `str`.
- substring constructor: Copies the portion of `str` that begins at the character position `pos` and spans `len` characters (or until the end of `str`, if either `str` is too short or if `len` is `string::npos`).
- from `c-string`: Copies the `null-terminated` character sequence (`C-string`) pointed by `s`.
- from buffer: Copies the first `n` characters from the array of characters pointed by `s`.
- fill constructor: Fills the string with `n` consecutive copies of character `c`.
- range constructor: Copies the sequence of characters in the range `[first, last)`, in the same order.
- initializer list: Copies each of the characters in `il`, in the same order.
- move constructor: Acquires the contents of `str`. `str` is left in an unspecified but valid state.

&emsp;&emsp;All constructors above support an object of member type `allocator_type` as additional optional argument at the end, which for string is not relevant.
&emsp;&emsp;Parameters:

- `str`: Another `string` object, whose value is either copied or acquired.
- `pos`: Position of the first character in `str` that is copied to the object as a substring. If this is greater than `str's` length, it throws `out_of_range`. **Note**: The first character in `str` is denoted by a value of `0` (not `1`).
- `len`: Length of the substring to be copied (if the string is shorter, as many characters as possible are copied). A value of `string::npos` indicates all characters until the end of `str`.
- `s`: Pointer to an array of characters (such as a `c-string`).
- `n`: Number of characters to copy.
- `c`: Character to fill the string with. Each of the `n` characters in the string will be initialized to a copy of this value.
- `first`, `last`: Input iterators to the initial and final positions in a range. The range used is `[first, last)`, which includes all the characters between `first` and `last`, including the character pointed by `first` but not the character pointed by `last`. The function template argument `InputIterator` shall be an input iterator type that points to elements of a type convertible to `char`. If `InputIterator` is an integral type, the arguments are casted to the proper types so that signature is used instead.
- `il`: An `initializer_list` object. These objects are automatically constructed from initializer list declarators.

``` cpp
#include <iostream>
#include <string>

int main () {
    std::string s0 ( "Initial string" );
    /* constructors used in the same order as described above */
    std::string s1;
    std::string s2 ( s0 );
    std::string s3 ( s0, 8, 3 );
    std::string s4 ( "A character sequence" );
    std::string s5 ( "Another character sequence", 12 );
    std::string s6a ( 10, 'x' );
    std::string s6b ( 10, 42 ); /* 42 is the ASCII code for '*' */
    std::string s7 ( s0.begin(), s0.begin() + 7 );
    std::cout << "s1: " << s1 << "\ns2: " << s2 << "\ns3: " << s3;
    std::cout << "\ns4: " << s4 << "\ns5: " << s5 << "\ns6a: " << s6a;
    std::cout << "\ns6b: " << s6b << "\ns7: " << s7 << '\n';
    return 0;
}
```

Output:

``` cpp
s1:
s2: Initial string
s3: str
s4: A character sequence
s5: Another char
s6a: xxxxxxxxxx
s6b: **********
s7: Initial
```