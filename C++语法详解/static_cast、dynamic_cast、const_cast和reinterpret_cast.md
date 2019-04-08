---
title: static_cast、dynamic_cast、const_cast和reinterpret_cast
date: 2019-02-07 15:20:55
tags:
---
### static_cast

&emsp;&emsp;该运算符把`expression`转换为`type-id`类型，但没有运行时类型检查来保证转换的安全性。

``` cpp
static_cast < type-id > ( expression )
```

&emsp;&emsp;它主要有如下几种用法：

- 用于类层次结构中基类(父类)和派生类(子类)之间指针或引用的转换。进行上行转换(把派生类的指针或引用转换成基类表示)是安全的；进行下行转换(把基类指针或引用转换成派生类表示)时，由于没有动态类型检查，所以是不安全的。
- 用于基本数据类型之间的转换，如把`int`转换成`char`，把`int`转换成`enum`。这种转换的安全性也要开发人员来保证。
- 把空指针转换成目标类型的空指针。
- 把任何类型的表达式转换成`void`类型。

&emsp;&emsp;注意，`static_cast`不能转换掉`expression`的`const`、`volatile`、或者`__unaligned`属性，也不是用来去掉`static`属性的。
&emsp;&emsp;`C++`中的`static_cast`执行非多态的转换，用于代替`C`中通常的转换操作，因此被做为显式类型转换使用。

``` cpp
int i;
float f = 166.71;
i = static_cast<int> ( f ); /* i is 166 */
```

&emsp;&emsp;`static_cast`也支持指向基类的指针和指向子类的指针之间的转换：

``` cpp
class A {
public:
    virtual void  a() {
        cout << 'a' << endl;
    }
​
    virtual void b() {
        cout << 'a' << endl;
    }
​
  private:
    int i;
};
​
class B: public A {
public:
    virtual void a() {
        cout << 'b' << endl;
    }
};
​
int main ( void ) {
    A *pa = new A();
    B *pb = static_cast<B *> ( pa );
    B *ppb = new B();
    A *ppa = static_cast<A *> ( ppb );
    return 0;
}
```

---

### dynamic_cast

&emsp;&emsp;其用法如下：

``` cpp
dynamic_cast < type-id > ( expression)
```

该运算符把`expression`转换成`type-id`类型的对象，`type-id`必须是类的指针、类的引用或者`void *`。如果`type-id`是类指针类型，那么`expression`也必须是一个指针；如果`type-id`是一个引用，那么`expression`也必须是一个引用。
&emsp;&emsp;`dynamic_cast`运算符可以在执行期决定真正的类型，如果`downcast`是安全的(也就是说如果基类指针或者引用确实指向一个派生类对象)，这个运算符会传回适当转型过的指针；如果`downcast`不安全，这个运算符会传回空指针(也就是说基类指针或者引用没有指向一个派生类对象)。
&emsp;&emsp;`dynamic_cast`主要用于类层次间的上行转换和下行转换，还可以用于类之间的交叉转换。
&emsp;&emsp;`dynamic_cast`将一个基类对象指针(或引用)`cast`到继承类指针时，`dynamic_cast`会根据基类指针是否真正指向继承类指针来做相应处理：对指针进行`dynamic_cast`，失败返回`null`，成功返回正常`cast`后的对象指针；对引用进行`dynamic_cast`，失败抛出一个异常，成功返回正常`cast`后的对象引用。在类层次间进行上行转换时，`dynamic_cast`和`static_cast`的效果是一样的；在进行下行转换时，`dynamic_cast`具有类型检查的功能，比`static_cast`更安全。

``` cpp
class B {
public:
    int m_iNum;
    virtual void foo();
};
​
class D: public B {
public:
    char *m_szName[100];
};
​
void func ( B *pb ) {
    D *pd1 = static_cast<D *> ( pb );
    D *pd2 = dynamic_cast<D *> ( pb );
}
```

在上面的代码段中，如果`pb`指向一个`D`类型的对象，`pd1`和`pd2`是一样的，并且对这两个指针执行`D`类型的任何操作都是安全的；但如果`pb`指向的是一个`B`类型的对象，那么`pd1`将是一个指向该对象的指针，对它进行`D`类型的操作将是不安全的(如访问`m_szName`)，而`pd2`将是一个空指针。
&emsp;&emsp;`B`要有虚函数，否则会编译出错；`static_cast`则没有这个限制。`B`中需要检测有虚函数的原因：类中存在虚函数，就说明它有想要让基类指针或引用指向派生类对象的情况，此时转换才有意义。这是由于运行时类型检查需要运行时类型信息，而这个信息存储在类的虚函数表中，只有定义了虚函数的类才有虚函数表。

---

### const_cast

&emsp;&emsp;`const_cast`是一种`C++`运算符，主要是用来去除复合类型中`const`和`volatile`属性(没有真正去除)。变量本身的`const`属性是不能去除的，要想修改变量的值，一般是去除指针(或引用)的`const`属性，再进行间接修改。

``` cpp
const_cast<type> ( expression )
```

通过`const_cast`运算符，也只能将`const type *`转换为`type *`，将`const type &`转换为`type &`。也就是说源类型和目标类型除了`const`属性不同，其他地方完全相同。

``` cpp
#include <iostream>
​
using namespace std;
​
void ConstTest1 ( void ) {
    const int a = 5;
    int *p;
    p = const_cast<int *> ( &a );
    ( *p )++;
    cout << a << endl;
    cout << *p << endl;
}
​
void ConstTest2 ( void ) {
    int i;
    cout << "please input a integer:";
    cin >> i;
    const int a = i;
    int &r = const_cast<int &> ( a );
    r++;
    cout << a << endl;
}
​
int main ( void ) {
    ConstTest1();
    ConstTest2();
    return 0;
}
```

---

### reinterpret_cast

&emsp;&emsp;`reinterpret_cast`是`C++`里的强制类型转换符：

``` cpp
reinterpret_cast < type-id > ( expression )
```

&emsp;&emsp;`reinterpret_cast`是为了映射到一个完全不同类型，这个关键词在我们需要把类型映射回原有类型时会用到它。如果我们映射到的类型仅仅是为了故弄玄虚和其他目的，这是所有映射中最危险的(这句话是`C++`编程思想中的原话)。
&emsp;&emsp;`static_cast`和`reinterpret_cast`操作符修改了操作数类型，它们不是互逆的：`static_cast`在编译时使用类型信息执行转换，在转换执行必要的检测(诸如指针越界计算、类型检查)，其操作数相对是安全的；`reinterpret_cast`仅仅是重新解释了给出的对象的比特模型而没有进行二进制转换：

``` cpp
int n = 9;
double d = static_cast < double > ( n );
```

上面的例子中，我们将一个变量从`int`转换到`double`，这些类型的二进制表达式是不同的。要将整数`9`转换到双精度数`9`，`static_cast`需要正确地为双精度数`d`补足比特位，其结果为`9.0`。而`reinterpret_cast`的行为却不同：

``` cpp
int n = 9;
double d = reinterpret_cast<double & > ( n );
```

这次结果有所不同，在进行计算以后，`d`包含无用值。这是因为`reinterpret_cast`仅仅是复制`n`的比特位到`d`，没有进行必要的分析。因此，你需要谨慎使用`reinterpret_cast`。