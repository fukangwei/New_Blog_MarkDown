---
title: 共用体union
categories: C语言语法详解
date: 2018-12-12 06:21:36
---
&emsp;&emsp;`union`的一些妙用如下：<!--more-->
&emsp;&emsp;1. 为了方便看懂代码。比如说写一个`3*3`的矩阵：

``` cpp
struct Matrix {
    union {
        struct {
            float _f11, _f12, _f13, _f21, _f22, _f23, _f31, _f32, _f33;
        };
        float f[3][3];
    } _matrix;
};

struct Matrix m;
```

这两者共同使用相同的空间，所以没有空间浪费，在需要整体用矩阵的时候可以用`m._matrix.f`(比如说传参或者是整体赋值等)；需要用其中的几个元素的时候可以用`m._matrix._f11`，这样可以避免用`m.f[0][0]`(这样不大直观，而且容易出错)。
&emsp;&emsp;2. 用在强制类型转换上(比强制类型转换更加容易看懂)。
&emsp;&emsp;1) 判断系统用的是`big endian`还是`little endian`：

``` cpp
#define TRUE  1
#define FALSE 0
#define BOOL  int

BOOL isBigEndian() {
    int i = 1; /* i = 0x00000001*/
    char c = * ( char* ) &i; /* 注意不能写成“char c = (char)i;” */
    return ( int ) c != i;
}
```

如果是`little endian`，那么`i = 1;`的内存从小到大依次放的是`0x01 0x00 0x00 0x00`，按照`i`的起始地址变成按照`char *`方式(`1`字节)存取，即得`c = 0x01`，反之亦然。也许看起来不是很清晰，下面来看一下这个：

``` cpp
BOOL isBigEndian() {
    union {
        int i;
        char c;
    } test;

    test.c = 2;
    return test.i != 2;
}
```

这里用的是`union`来控制这个共享布局，有个知识点就是`union`里面的成员`c`和`i`都是从低地址开始对齐的。同样可以得到如此结果，而且不用转换，清晰一些。
&emsp;&emsp;2) 将`little endian`下的`long long`类型的值换成`big endian`类型的值。已知系统提供了下面的`api`：`long htonl(long lg);`，其作用是把所有的字节序换成大端字节序，得出下面的做法：

``` cpp
long long htonLL ( long long lg ) {
    union {
        struct {
            long low;
            long high;
        } val_1;
        long long val_2;
    } val_arg, val_ret;

    if ( isBigEndian() ) {
        return lg;
    }

    val_arg.val_2 = lg;
    val_ret.val_1.low = htonl ( val_arg.val_1.high );
    val_ret.val_1.high = htonl ( val_arg.val_1.low );
    return val_ret.val_2;
}
```

&emsp;&emsp;3) 为了理解`C++`类的布局，再看下面一个例子。有如下类：

``` cpp
class Test {
public:
    float getFVal() {
        return f;
    }

private:
    int i;
    char c;
    float f;
};

Test t;
```

不需要在类`Test`中增加代码，完成对对象中的`f`赋值`7.0f`：

``` cpp
class Test_Cpy {
public:
    float getVal() {
        return f;
    }

    float setVal ( float f ) {
        this->f = f;
    }

private:
    int i;
    char c;
    float f;
};

int main() {
    Test t;

    union {
        Test t1, Test_Cpy t2;
    } test;

    test.t2.setVal ( 7.0f );
    t = test.t1;
    assert ( t.getVal() == 7.0f );
    return 0;
}
```

因为在增加类的成员函数时候，那个类的对象的布局基本不变。因此可以写一个与`Test`类一样结构的类`Test_Cpy`，多了一个成员函数`setVal`，再用`uinon`结构对齐，就可以给私有变量赋值了(这种方法在有虚基类和虚函数机制时可能失灵，故不可移植)。这个例子在实际中没有用途，只是用来考察内存布局的使用而已。
&emsp;&emsp;`union`在操作系统底层的代码中用的比较多，因为它在内存共享布局上方便且直观，简化了设计。