---
title: this指针
categories: C++语法详解
abbrlink: 2b153c25
date: 2019-02-05 18:53:49
---
&emsp;&emsp;对象的`this`指针并不是对象本身的一部分，不会影响`sizeof(对象)`的结果。`this`作用域是在类内部，当类的非静态成员函数中访问类的非静态成员时，编译器会自动将对象本身的地址作为一个隐含参数传递给函数。也就是说，即使你没有写上`this`指针，编译器在编译时也会加上`this`，它作为非静态成员函数的隐含形参，对各成员的访问均通过`this`进行。例如，调用`date.SetMonth(9) <--> SetMonth(&date, 9)`，`this`帮助完成了这一转换。<!--more-->
&emsp;&emsp;一种情况就是，在类的非静态成员函数中返回类对象本身时，直接使用`return *this`；另外一种情况是，当成员函数的参数与成员变量名相同时，要写成`this->n = n`，不能写成`n = n`。
&emsp;&emsp;`this`指针存在于类的成员函数中，指向被调用函数所在的类实例的地址。根据以下程序来说明`this`指针：

``` cpp
#include "iostream"
​
using namespace std;
​
class Point {
    int x, y;
public:
    Point ( int a, int b ) {
        x = a;
        y = b;
    }
​
    void MovePoint ( int a, int b ) {
        x += a;
        y += b;
    }
​
    void print() {
        cout << "x = " << x << ", y = " << y << endl;
    }
};
​
int main( ) {
    Point point1 ( 10, 10 );
    point1.MovePoint ( 2, 2 );
    point1.print();
}
```

当对象`point1`调用`MovePoint(2, 2)`时，程序将`point1`对象的地址传递给了`this`指针。`MovePoint`函数的原型应是：

``` cpp
void MovePoint ( Point *this, int a, int b );
```

第一个参数是指向该类对象的一个指针，在定义成员函数时没看见它，是因为这个参数在类中是隐含的。这样，`point1`的地址传递给了`this`，所以`MovePoint`函数便显式地写成了：

``` cpp
void MovePoint ( int a, int b ) {
    this->x += a;
    this->y += b;
}
```

于是，`point1`调用该函数后，它的的数据成员被调用并更新了值，该函数过程可写成了：

``` cpp
point1.x += a;
point1.y += b;
```

&emsp;&emsp;关于`this`指针的一个经典回答：当你进入一个房子后，你可以看见桌子、椅子、地板等，但是房子你是看不到全貌了。对于一个类的实例来说，你可以看到它的成员函数、成员变量，但是实例本身呢？`this`是一个指针，它时时刻刻指向你这个实例本身。
&emsp;&emsp;`this`只能在成员函数中使用，全局函数、静态函数都不能使用`this`。实际上，成员函数默认第一个参数为`T * const this`：

``` cpp
class A {
public:
    int func ( int p ) {
    }
};
```

`func`的原型在编译器看来应该是`int func(A * const this,int p);`。由此可见，`this`在成员函数的开始前构造，在成员函数的结束后清除。这个生命周期同任何一个函数的参数是一样的，没有任何区别。当调用一个类的成员函数时，编译器将类的指针作为函数的`this`参数传递进去：

``` cpp
A a;
a.func ( 10 );
```

此处，编译器将会编译成`A::func(&a, 10);`。