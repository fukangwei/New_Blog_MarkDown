---
title: const对象
date: 2021-07-03 06:04:58
categories: C++语法详解
---
&emsp;&emsp;在`C++`中，`const`也可以用来修饰对象，称为`常对象`。<!--more-->
&emsp;&emsp;一旦将对象定义为常对象之后，就只能调用类的`const`成员，包括`const`成员变量和`const`成员函数。

### 定义常对象

&emsp;&emsp;定义常对象的语法如下：

``` cpp
const class object ( params );
class const object ( params );
```

&emsp;&emsp;当然也可以定义`const`指针：

``` cpp
const class* p = new class ( params );
class const* p = new class ( params );
```

`class`为类名，`object`为对象名，`params`为实参列表，`p`为指针名。两种方式定义出来的对象都是常对象。

### 代码实例

&emsp;&emsp;代码实例：

``` cpp
#include <iostream>
using namespace std;

class Student {
  public:
    Student ( char* name, int age );

  public:
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

int main() {
    const Student stu ( ( char * ) "小明", 15 );
    // stu.show(); /* error */
    cout << stu.getname() << "的年龄是" << stu.getage() << endl;
    const Student* pstu = new Student ( ( char * ) "李磊", 16 );
    // pstu -> show(); /* error */
    cout << pstu->getname() << "的年龄是" << pstu->getage() << endl;
    return 0;
}
```

执行结果：

``` cpp
小明的年龄是15
李磊的年龄是16
```