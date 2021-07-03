---
title: explicit关键字
categories: C++语法详解
date: 2019-02-05 20:50:05
---
### 类型转换

&emsp;&emsp;`explicit`关键字只能用于修饰只有一个参数的构造函数，它的作用是表明该构造函数是显式的。<!--more-->
&emsp;&emsp;跟`explicit`相对应的一个关键字是`implicit`，构造函数默认情况下即声明为`implicit`。
&emsp;&emsp;那么`explicit`和`implicit`有什么区别呢？

``` cpp
class CxString {
  public:
    char* _pstr;
    int _size;

    CxString ( int size ) {
        _size = size;
        _pstr = malloc ( size + 1 );
        memset ( _pstr, 0, size + 1 );
    }

    CxString ( const char* p ) {
        int size = strlen ( p );
        _pstr = malloc ( size + 1 );
        strcpy ( _pstr, p );
        _size = strlen ( _pstr );
    }
};

CxString string1 ( 24 );     /* 这样是OK的，为CxString预分配24字节的大小的内存 */
CxString string2 = 10;       /* 这样是OK的，为CxString预分配10字节的大小的内存 */
CxString string3;            /* 这样是不行的，因为没有默认构造函数             */
CxString string4 ( "aaaa" ); /* 这样是OK的                                   */
CxString string5 = "bbb";    /* 这样也是OK的，调用的是CxString(const char *p) */
string1 = 2;                 /* 这样也是OK的，为CxString预分配2字节的大小的内存 */
string2 = 3;                 /* 这样也是OK的，为CxString预分配3字节的大小的内存 */
```

&emsp;&emsp;如果的构造函数只有一个参数时，那么在编译时就会有一个缺省的转换操作：将该构造函数对应数据类型的数据转换为该类对象。
&emsp;&emsp;对于`CxString string2 = 10;`这段代码，编译器自动将整型数据转换为`CxString`类对象：

``` cpp
CxString string2 ( 10 );
```

### explicit的作用

&emsp;&emsp;`explicit`的作用就是防止构造函数的隐式自动转换：

``` cpp
class CxString {
  public:
    char* _pstr;
    int _size;
    explicit CxString ( int size ) {
        _size = size;
        /* 代码同上，省略 */
    }

    CxString ( const char* p ) {
        /* 代码同上，省略 */
    }
};

CxString string1 ( 24 );     /* 这样是OK的                                  */
CxString string2 = 10;       /* 这样是不行的，因为explicit关键字取消了隐式转换 */
CxString string3;            /* 这样是不行的，因为没有默认构造函数             */
CxString string4 ( "aaaa" ); /* 这样是OK的                                   */
CxString string5 = "bbb";    /* 这样也是OK的，调用的是CxString(const char *p) */
string1 = 2;                 /* 这样也是不行的，因为取消了隐式转换             */
string2 = 3;                 /* 这样也是不行的，因为取消了隐式转换             */
```

### 注意点

&emsp;&emsp;`explicit`只对有一个参数的构造函数有效，如果构造函数参数大于一个时，是不会产生隐式转换的。
&emsp;&emsp;但是当除了第一个参数以外的其他参数都有默认值时，`explicit`依然有效。