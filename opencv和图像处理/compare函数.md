---
title: compare函数
categories: opencv和图像处理
abbrlink: 370e2cb1
date: 2018-12-30 14:42:07
---
&emsp;&emsp;`compare`函数原型如下(定义在头文件`core.hpp`中)：

``` c
void compare (InputArray src1, InputArray src2, OutputArray dst, int cmpop);
```

函数作用是按照指定的操作`cmpop`，比较输入的`src1`和`src2`中的元素，输出结果到`dst`中。

- `src1`：原始图像`1`(必须是单通道)或者一个数值，比如是一个`Mat`或者一个单纯的数字`n`。
- `src2`：原始图像`2`(必须是单通道)或者一个数值，比如是一个`Mat`或者一个单纯的数字`n`。
- `dst`：结果图像，类型是`CV_8UC1`，即单通道`8`位图，大小和`src1`和`src2`中最大的那个一样。比较结果为真的地方值是`255`，否则为`0`。
- `cmpop`：操作类型，有以下几种：

``` cpp
enum {
    CMP_EQ = 0, /* 相等     */
    CMP_GT = 1, /* 大于     */
    CMP_GE = 2, /* 大于等于 */
    CMP_LT = 3, /* 小于     */
    CMP_LE = 4, /* 小于等于 */
    CMP_NE = 5  /* 不相等   */
};
```

从参数的要求可以看出，`compare`函数只对以下三种情况进行比较：
&emsp;&emsp;1. `array`和`array`：此时输入的`src1`和`src2`必须是相同大小的单通道图，否则没办法进行比较了。计算过程就是：

``` c
dst(i) = src1(i) cmpop src2(i)
```

也就是对`src1`和`src2`逐像素进行比较。
&emsp;&emsp;2. `array`和`scalar`：此时`array`仍然要求是单通道图，大小无所谓，因为`scalar`只是一个单纯的数字而已。比较过程是把`array`中的每个元素逐个和`scalar`进行比较，所以此时的`dst`大小和`array`是一样的。计算过程如下：

``` c
dst(i) = src1(i) cmpop scalar
```

&emsp;&emsp;3. `scalar`和`array`：这个就是上面的反过程了，只是比较运算符`cmpop`左右的参数顺序不一样了而已。计算过程如下：

``` c
dst(i) = scalar cmpop src2(i)
```

&emsp;&emsp;当需要从一幅图像中找出那些特定像素值的像素时，可以用这个函数。类似于`threshold`函数，但是`threshold`函数是对某个区间内的像素值进行操作，`compare`函数则可以只是对某一个单独的像素值进行操作。例如要从图像中找出像素值为`50`的像素点，可以这样做：

``` cpp
cv::Mat result;
cv::compare ( image, 50, result, cv::CMP_EQ );
```