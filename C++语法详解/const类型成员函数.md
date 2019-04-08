---
title: const类型成员函数
date: 2019-02-05 18:46:18
tags:
---
&emsp;&emsp;在`C++`中，若一个变量声明为`const`类型，则试图修改该变量的值的操作都被视编译错误：

``` cpp
const char blank = '';
blank = '\n'; /* 错误 */
```

&emsp;&emsp;面向对象程序设计中，为了体现封装性，通常不允许直接修改类对象的数据成员。若要修改类对象，应调用公有成员函数来完成。为了保证`const`对象的常量性，编译器必须区分不安全与安全的成员函数(即区分试图修改类对象与不修改类对象的函数)：

``` cpp
const Screen blankScreen;
blankScreen.display(); /* 对象的读操作 */
blankScreen.set ( '*' ); /* 错误：const类对象不允许修改 */
```

在`C++`中，只有被声明为`const`的成员函数才能被`const`类对象调用。要声明一个`const`类型的类成员函数，只需要在成员函数参数列表后加上关键字`const`：

``` cpp
class Screen {
public:
    char get() const;
};
```

在类体之外定义`const`成员函数时，还必须加上`const`关键字：

``` cpp
char Screen::get() const {
    return _screen[_cursor];
}
```

若将成员成员函数声明为`const`，则该函数不允许修改类的数据成员：

``` cpp
class Screen {
public:
    int ok() const {
        return _cursor;
    }
​
    int error ( intival ) const {
        _cursor = ival;
    }
};
```

在上面成员函数的定义中，`ok`的定义是合法的，`error`的定义则是非法的。
&emsp;&emsp;值得注意的是，把一个成员函数声明为`const`可以保证这个成员函数不修改数据成员。但如果数据成员是指针，则`const`成员函数并不能保证不修改指针指向的对象，编译器不会把这种修改检测为错误：

``` cpp
class Name {
public:
    void setName ( const string &s ) const;
private:
    char *m_sName;
};
​
void setName ( const string &s ) const {
    m_sName = s.c_str(); /* 错误！不能修改m_sName */
​
    for ( int i = 0; i < s.size(); ++i ) {
        m_sName[i] = s[i]; /* 不好的风格，但不是错误的 */
    }
}
```

虽然`m_Name`不能被修改，但`m_sName`是`char *`类型，`const`成员函数可以修改其所指向的字符。`const`成员函数可以被具有相同参数列表的非`const`成员函数重载：

``` cpp
class Screen {
public:
    char get ( int x, int y );
    char get ( int x, int y ) const;
};
```

在这种情况下，类对象的常量性决定调用哪个函数：

``` cpp
const Screen cs;
Screen cc2;
char ch = cs.get ( 0, 0 ); /* 调用const成员函数 */
ch = cs2.get ( 0, 0 ); /* 调用非const成员函数 */
```

- `const`成员函数可以访问非`const`对象的非`const`数据成员、`const`数据成员，也可以访问`const`对象内的所有数据成员。
- 非`const`成员函数可以访问非`const`对象的非`const`数据成员、`const`数据成员，但不可以访问`const`对象的任意数据成员。
- 作为一种良好的编程风格，在声明一个成员函数时，若该成员函数并不对数据成员进行修改操作，应尽可能将该成员函数声明为`const`成员函数。
- `const`对象必须进行初始化，而且不能被更新。`const`对象只能调用它的`const`成员函数。