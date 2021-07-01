---
title: class和struct的区别
categories: C++语法详解
date: 2018-12-27 09:11:47
---
### C++的struct

&emsp;&emsp;`C++`保留了`C`语言的`struct`关键字，并且加以扩充。<!--more-->
&emsp;&emsp;在`C`语言中，`struct`只能包含成员变量，不能包含成员函数。而在`C++`中，`struct`类似于`class`，既可以包含成员变量，又可以包含成员函数。

### struct和class

&emsp;&emsp;`C++`中的`struct`和`class`基本是通用的，唯有几个细节不同：

- 使用`class`时，类中的成员默认都是`private`属性的；而使用`struct`时，结构体中的成员默认都是`public`属性的。
- `class`默认是`private`继承，而`struct`默认是`public`继承。
- `class`可以使用模板，而`struct`不能。

### 代码实例

&emsp;&emsp;代码实例：

``` cpp
#include <iostream>

using namespace std;

struct Student {
    Student ( char* name, int age );
    void show();

    char* m_name;
    int m_age;
};

Student::Student ( char* name, int age ) : m_name ( name ), m_age ( age ) {
}

void Student::show() {
    cout << m_name << "的年龄是" << m_age << endl;
}

int main() {
    Student stu ( ( char * ) "小明", 15 );
    stu.show();
    Student* pstu = new Student ( ( char * ) "李华", 16 );
    pstu->show();
    return 0;
}
```

执行结果：

``` cpp
小明的年龄是15
李华的年龄是16
```