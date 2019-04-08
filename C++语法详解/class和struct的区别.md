---
title: class和struct的区别
date: 2018-12-27 09:11:47
tags:
---
&emsp;&emsp;在`C++`中，`class`和`struct`做类型定义时只有两点区别：

- 默认继承权限：如果不明确指定，来自`class`的继承按照`private`继承处理，来自`struct`的继承按照`public`继承处理。
- 成员的默认访问权限：`class`的成员默认是`private`权限，`struct`默认是`public`权限。

除了这两点，`class`和`struct`基本就是一个东西，语法上没有任何其它区别。
&emsp;&emsp;下面列举的说明可能有助于澄清一些常见的关于`struct`和`class`的错误认识：

- 都可以有成员函数：包括各类构造函数、析构函数、重载的运算符、友元类、友元结构、友元函数、虚函数、纯虚函数和静态函数。
- 都可以有一大堆`public/private/protected`修饰符在里边。
- 虽然不再提倡这种风格，但语法上二者都可以使用大括号的方式初始化，例如`A a = {1, 2, 3};`。不管`A`是个`struct`还是个`class`，前提是这个类/结构足够简单，比如所有的成员都是`public`的，所有的成员都是简单类型，没有显式声明的构造函数。
- 都可以进行复杂的继承甚至多重继承，一个`struct`可以继承自一个`class`，反之亦可；一个`struct`可以同时继承`5`个`class`和`5`个`struct`，虽然这样做不太好。
- 如果说`class`的设计需要注意`OO`的原则和风格，那么没任何理由说设计`struct`就不需要注意。
- 再次说明，以上所有说法都是指在`C++`语言中。`C`语言里根本没有`class`，而`C`的`struct`也只是个包装数据的语法机制。

&emsp;&emsp;最后，作为语言的两个关键字，除去定义类型时有上述区别之外，另外还有一点点区别：`class`这个关键字还用于定义模板参数，就像`typename`，但关键字`struct`不用于定义模板参数。

- 关于大括号初始化：`class`和`struct`如果定义了构造函数的话，都不能用大括号进行初始化；如果没有定义构造函数，`struct`可以用大括号初始化；如果没有定义构造函数，且所有成员变量全是`public`的话，可以用大括号初始化。
- 关于继承方式：`class`继承默认是`private`继承，而`struct`继承默认是`public`继承。
- 关于模版：在模版中，类型参数前面可以使用`class`或`typename`；如果使用`struct`，则含义不同，`struct`后面跟的是`non-type template parameter`，而`class`或`typename`后面跟的是类型参数。
- 关于`this`指针：`class`中有个默认的`this`指针，而`struct`没有。

---
&emsp;&emsp;`C++`结构体提供了比`C`语言结构体更多的功能，例如默认构造函数、复制构造函数以及运算符重载，这些功能使得结构体对象能够方便地传值。例如定义一个结构体，然后将其作为`vector`的元素类型，要使用的话，就需要实现上述三个函数，否则就只能用指针了。代码如下所示：

``` cpp
#include <iostream>
#include <vector>
​
using namespace std;
​
struct ST {
    int a;
    int b;
​
    ST() { /* 默认构造函数 */
        a = 0;
        b = 0;
    }
​
    void set ( ST *s1, ST *s2 ) { /* 赋值函数 */
        s1->a = s2->a;
        s1->b = s2->b;
    }
​
    ST &operator= ( const ST &s ) { /* 重载运算符 */
        set ( this, ( ST * ) &s )
    }
​
    ST ( const ST &s ) { /* 复制构造函数 */
        *this = s;
    }
};
​
int main ( void ) {
    ST a ; /* 调用默认构造函数 */
    vector<ST> v;
    v.push_back ( a ); /* 调用复制构造函数 */
    ST s = v.at ( 0 ); /* 调用“=”函数 */
    cout << s.a << " " << s.b << endl;
    cin >> a.a;
    return 0;
}
```