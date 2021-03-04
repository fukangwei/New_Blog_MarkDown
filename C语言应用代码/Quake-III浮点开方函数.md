---
title: Quake-III浮点开方函数
categories: C语言应用代码
date: 2019-02-07 10:03:49
mathjax: true
---
&emsp;&emsp;`QUAKE`的开发商`ID SOFTWARE`遵守`GPL`协议，公开了`QUAKE-III`的源代码，让世人有幸目睹`Carmack`传奇的`3D`引擎的源码，名称为`quake3-1.32b-source.zip`。<!--more-->
&emsp;&emsp;我们知道，越底层的函数，调用越频繁。`3D`引擎归根到底还是数学运算，那么找到最底层的数学运算函数(`game/code/q_math.c`)，必然是精心编写的。在`game/code/q_math.c`里发现了这样一段代码，它的作用是将一个数开平方并取倒，经测试这段代码比`(float)(1.0 / sqrt(x))`快`4`倍：

``` cpp
float Q_rsqrt ( float number ) {
    long i;
    float x2, y;
    const float threehalfs = 1.5F;
    x2 = number * 0.5F;
    y = number;
    i = * ( long * ) &y; /* evil floating point bit level hacking */
    i = 0x5f3759df - ( i >> 1 ); /* what the fuck? */
    y = * ( float * ) &i;
    y = y * ( threehalfs - ( x2 * y * y ) ); /* 1st iteration */
    // y = y * ( threehalfs - ( x2 * y * y ) ); // 2nd iteration, this can be removed
#ifndef Q3_VM
#ifdef __linux__
    assert ( !isnan ( y ) ); /* bk010122 - FPE? */
#endif
#endif
    return y;
}
```

&emsp;&emsp;这个简洁的函数最核心的部分就是标注了`what the fuck?`的一句`i = 0x5f3759df - ( i >> 1 );`，再加上`y = y * ( threehalfs - ( x2 * y * y ) );`，两句话就完成了开方运算！而且注意到，核心那句是定点移位运算，速度极快！特别在很多没有乘法指令的`RISC`结构`CPU`上，这样做是极其高效的。
&emsp;&emsp;算法的原理其实不复杂，就是牛顿迭代法，用$x - \frac{f(x)}{f'(x)}$来不断的逼近$f(x) = a$的根。简单来说比如求平方根：

\begin{aligned}
f(x) = x^2 = a \\
f'(x) = 2x \\
\frac{f(x)}{f'(x)} = \frac{x}{2} \notag
\end{aligned}

把$f(x)$代入$x - \frac{f(x)}{f'(x)}$后有$(x+\frac{a}{x})/2$。现在我们令`a`为`5`，选一个猜测值(比如`2`)，那么可以这么算：`5/2 = 2.5；(2.5 + 2)/2 = 2.25；5/2.25 = xxx；(2.25 + xxx)/2 = xxxx`。这样反复迭代下去，结果必定收敛于`sqrt(5)`。但是卡马克(`quake3`作者)真正厉害的地方是，他选择了一个神秘的常数`0x5f3759df`来计算那个猜测值。就是加注释的那一行，那一行算出的值非常接近`1/sqrt(n)`，这样只需要`2`次牛顿迭代就可以达到所需要的精度。
&emsp;&emsp;普渡大学的数学家`Chris Lomont`采用暴力的方法，找到一个比卡马克数字要好上那么一丁点的数字，这个数字就是`0x5f375a86`。`Lomont`为此写下一篇论文`Fast Inverse Square Root`。论文下载地址为`http://www.math.purdue.edu/~clomont/Math/Papers/2003/InvSqrt.pdf`和`http://www.matrix67.com/data/InvSqrt.pdf`。最精简的`1/sqrt`函数如下：

``` cpp
float InvSqrt ( float x ) {
    float xhalf = 0.5f * x;
    int i = * ( int * ) &x; /* get bits for floating VALUE */
    i = 0x5f375a86 - ( i >> 1 ); /* gives initial guess y0 */
    x = * ( float * ) &i; /* convert bits BACK to float */
    x = x * ( 1.5f - xhalf * x * x ); /* Newton step, repeating increases accuracy */
    return x;
}
```