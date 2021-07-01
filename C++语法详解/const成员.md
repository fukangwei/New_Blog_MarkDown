---
title: const成员
date: 2021-07-03 05:54:56
categories: C++语法详解
---
### const成员变量

&emsp;&emsp;`const`成员变量的用法和普通`const`变量的用法相似，只需要在声明时加上`const`关键字。<!--more-->
&emsp;&emsp;初始化`const`成员变量只有一种方法，就是通过构造函数的初始化列表。

### const成员函数

&emsp;&emsp;`const`成员函数也称为`常成员函数`，可以使用类中的所有成员变量，但是不能修改它们的值。
&emsp;&emsp;常成员函数需要在声明和定义的时候在函数头部的结尾加上`const`关键字：

``` cpp
class Student {
  public:
    Student ( char* name, int age );
    char* getname() const;
    int getage() const;

  private:
    char* m_name;
    int m_age;
};

Student::Student ( char* name, int age ) : m_name ( name ), m_age ( age ) {
}

char* Student::getname() const {
    return m_name;
}

int Student::getage() const {
    return m_age;
}
```