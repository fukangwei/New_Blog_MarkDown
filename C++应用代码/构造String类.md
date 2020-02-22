---
title: 构造String类
categories: C++应用代码
date: 2018-12-16 13:07:07
---
&emsp;&emsp;编写类`String`的构造函数、析构函数和赋值函数，已知类`String`的原型为：<!--more-->

``` cpp
class String {
  public:
    String ( const char* str = NULL ); /* 普通构造函数 */
    String ( const String& other ); /* 拷贝构造函数 */
    ~String ( void ); /* 析构函数 */
    String& operate = ( const String& other ); /* 赋值函数 */
  private:
    char* m_data; /* 用于保存字符串 */
};
```

代码如下：

``` cpp
String::String ( const char* str ) { /* 普通构造函数 */
    if ( str == NULL ) {
        m_data = new char[1]; /* 对空字符串自动申请存放结束标志“\0” */
        *m_data = '\0';
    } else {
        int length = strlen ( str );
        m_data = new char[length + 1];
        strcpy ( m_data, str );
    }
}

String::~String ( void ) { /* String的析构函数 */
    delete [] m_data;
}

String::String ( const String& other ) { /* 拷贝构造函数 */
    int length = strlen ( other.m_data );
    m_data = new char[length + 1];
    strcpy ( m_data, other.m_data );
}

String& String::operate = ( const String& other ) { /* 赋值函数 */
    if ( this == &other ) { /* 检查自赋值 */
        return *this;
    }

    delete [] m_data; /* 释放原有的内存资源 */
    int length = strlen ( other.m_data );
    m_data = new char[length + 1];
    strcpy ( m_data, other.m_data );
    return *this; /* 返回本对象的引用 */
}
```