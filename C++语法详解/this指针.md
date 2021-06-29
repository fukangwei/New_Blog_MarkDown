---
title: this指针
categories: C++语法详解
date: 2019-02-05 18:53:49
---
### this指针

&emsp;&emsp;在`C++`中，每一个对象都能在成员函数内部，通过`this`指针来访问自己的地址：<!--more-->

``` cpp
#include <iostream>

using namespace std;

class Box {
  public:
    Box* get_address() {
        return this;
    }
};

int main() {
    Box box1;
    Box* p = box1.get_address();
    cout << "p is " << p << endl;
    return 0;
}
```

执行结果：

``` cpp
p is 0x7ffeacc4ed5f
```

### 代码实例

&emsp;&emsp;代码实例：

``` cpp
#include <iostream>

using namespace std;

class Box {
  public:
    Box ( double l = 2.0, double b = 2.0, double h = 2.0 ) {
        cout << "Constructor called." << endl;
        length = l;
        breadth = b;
        height = h;
    }

    double Volume() {
        return length * breadth * height;
    }

    int compare ( Box box ) {
        return this->Volume() > box.Volume();
    }

  private:
    double length;
    double breadth;
    double height;
};

int main ( void ) {
    Box Box1 ( 3.3, 1.2, 1.5 );
    Box Box2 ( 8.5, 6.0, 2.0 );

    if ( Box1.compare ( Box2 ) ) {
        cout << "Box2 is smaller than Box1" << endl;
    } else {
        cout << "Box2 is equal to or larger than Box1" << endl;
    }

    return 0;
}
```

执行结果：

``` cpp
Constructor called.
Constructor called.
Box2 is equal to or larger than Box1
```

### this详解

&emsp;&emsp;实际上，成员函数默认第一个参数为`T * const this`。
&emsp;&emsp;类`A`如下：

``` cpp
class A {
  public:
    int func ( int p ) {
    }
};
```

`func`的原型在编译器看来应该是`int func ( A * const this, int p );`。当调用一个成员函数时，编译器将对象的地址作为函数的`this`参数传递进去：

``` cpp
A a;
// 如下2行代码相等
a.func ( 10 );
A::func ( &a, 10 );
```

### 注意点

- 在类的非静态成员函数中返回类对象本身时，直接使用`return *this`。
- 当成员函数的参数与成员变量名称相同时，要写成`this->n = n`，不能写成`n = n`。
- `this`只能在成员函数中使用，全局函数、静态函数都不能使用`this`。