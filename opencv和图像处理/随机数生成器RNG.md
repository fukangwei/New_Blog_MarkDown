---
title: 随机数生成器RNG
categories: opencv和图像处理
date: 2018-12-30 18:43:42
---
&emsp;&emsp;`C`语言和`C++`中产生随机数的方法(`rand`和`srand`等)在`OpenCV`中仍可以用。此外，`OpenCV`还特地编写了`C++`的随机数类`RNG`，`C`的随机数类`CvRNG`，以及一些相关的函数。注意如下说明：<!--more-->

- 关键字前带`cv`的都是`C`语言里的写法，不带`cv`的是`C++`里的写法。比如`CvRNG`和`RNG`，其本质都是一样的。
- 计算机产生的随机数都是伪随机数，是根据种子`seed`和特定算法计算出来的。所以只要种子一定，算法一定，产生的随机数是相同的。
- 要想产生完全重复的随机数，可以用系统时间做种子。在`OpenCV`中使用`GetTickCount`，`C`语言中用`time`。

### RNG

&emsp;&emsp;`RNG`类是`opencv`里`C++`的随机数产生器。它可产生一个`64`位的`int`随机数。目前可按均匀分布和高斯分布产生随机数。随机数的产生采用的是`Multiply-With-Carry`算法和`Ziggurat`算法。
&emsp;&emsp;`RNG`可以产生如下随机数：

- `RNG(int seed)`：使用种子`seed`产生一个`64`位随机整数，默认`-1`。
- `RNG::uniform()`：产生一个均匀分布的随机数。
- `RNG::gaussian()`：产生一个高斯分布的随机数。
- `RNG::uniform(a, b)`：返回一个`[a, b)`范围的均匀分布的随机数，`a`和`b`的数据类型要一致，而且必须是`int`、`float`、`double`中的一种，默认是`int`。
- `RNG::gaussian(σ)`：返回一个均值为`0`，标准差为`σ`的随机数。如果要产生均值为`λ`，标准差为`σ`的随机数，可以使用`λ + RNG::gaussian(σ)`。

&emsp;&emsp;代码示例如下：

``` cpp
RNG rng; /* 创建RNG对象，使用默认种子“-1” */
int N1 = rng; /* 产生64位整数 */
/* 总是得到double类型数据0.000000，因为会调用uniform(int, int)，只会取整数，所以只产生0 */
double N1a = rng.uniform ( 0, 1 );
/* 产生[0,1)范围内均匀分布的double类型数据 */
double N1b = rng.uniform ( ( double ) 0, ( double ) 1 );
/* 产生[0,1)范围内均匀分布的float类型数据，注意被自动转换为double了 */
double N1c = rng.uniform ( 0.f, 1.f );
/* 产生[0,1)范围内均匀分布的double类型数据 */
double N1d = rng.uniform ( 0., 1. );
/* 产生符合均值为0，标准差为2的高斯分布的随机数 */
double N1g = rng.gaussian ( 2 );
```

其实`rng`既是一个`RNG`对象，也是一个随机整数。

### 返回下一个随机数

&emsp;&emsp;上面一次只能返回一个随机数，实际上系统已经生成一个随机数组。如果我们要连续获得随机数，没有必要重新定义一个`RNG`类，只需要取出随机数组的下一个随机数即可。

- `RNG::next`：返回下一个`64`位随机整数。
- `RNG::operator`：返回下一个指定类型的随机数。

&emsp;&emsp;代码示例如下：

``` cpp
RNG rng;
int N2 = rng.next(); /* 返回下一个随机整数，即“N1.next();” */
/* 返回下一个指定类型的随机数 */
int N2a = rng.operator uchar(); /* 返回下一个无符号字符数 */
int N2b = rng.operator schar(); /* 返回下一个有符号字符数 */
int N2c = rng.operator ushort(); /* 返回下一个无符号短型 */
int N2d = rng.operator short int(); /* 返回下一个短整型数 */
int N2e = rng.operator int(); /* 返回下一个整型数 */
int N2f = rng.operator unsigned int(); /* 返回下一个无符号整型数 */
int N2g = rng.operator float(); /* 返回下一个浮点数 */
int N2h = rng.operator double(); /* 返回下一个double型数 */
int N2i = rng.operator ()(); /* 和“rng.next()”等价 */
int N2j = rng.operator ()(100); /* 返回[0, 100)范围内的随机数 */
```

### 用随机数填充矩阵RNG::fill

&emsp;&emsp;函数原型如下：

``` cpp
void fill ( InputOutputArray mat, int distType, InputArray a,
            InputArray b, bool saturateRange = false);
```

- `mat`：输入输出矩阵，最多支持`4`通道，超过`4`通道先用`reshape`改变结构。
- `distType`：`UNIFORM`或`NORMAL`，表示均匀分布和高斯分布。
- `a`：如果`disType`是`UNIFORM`，则`a`表示为下界(闭区间)；如果`disType`是`NORMAL`，则`a`表示均值。
- `b`：如果`disType`是`UNIFORM`，则`b`表示为上界(开区间)；如果`disType`是`NORMAL`，则`b`标准差。
- `saturateRange`：只针对均匀分布有效。当为真的时候，会先把产生随机数的范围变换到数据类型的范围，再产生随机数；如果为假，会先产生随机数，再进行截断到数据类型的有效区间。

``` cpp
/* 产生[1, 1000)均匀分布的int随机数填充fillM */
Mat_<int> fillM ( 3, 3 );
rng.fill ( fillM, RNG::UNIFORM, 1, 1000 );
cout << "filM = " << fillM << endl;

Mat fillM1 ( 3, 3, CV_8U );
rng.fill ( fillM1, RNG::UNIFORM, 1, 1000, TRUE );
cout << "filM1 = " << fillM1 << endl;

Mat fillM2 ( 3, 3, CV_8U );
rng.fill ( fillM2, RNG::UNIFORM, 1, 1000, FALSE );
cout << "filM2 = " << fillM2 << endl;

/* fillM1产生的数据都在[0, 255)内，且小于255                                 */
/* fillM2产生的数据虽然也在同样范围内，但是由于用了截断操作，所以很多数据都是255 */
/* 产生均值为1，标准差为3的随机double数填进fillN                              */
Mat_<double>fillN ( 3, 3 );
rng.fill ( fillN, RNG::NORMAL, 1, 3 );
cout << "filN = " << fillN << endl;
```

### randu

&emsp;&emsp;`randu`的作用是返回均匀分布的随机数，填入数组或矩阵：

``` cpp
randu ( dst, low, high );
```

参数`dst`是输出数组或矩阵，`low`是区间下界(闭区间)，`high`是区间上界(开区间)。

``` cpp
Mat_<int> randuM ( 3, 3 );
randu ( randuM, Scalar ( 0 ), Scalar ( 255 ) );
cout << "randuM = " << randuM << endl;
```

其实`randu`和`rng.fill`功能是类似的，只不过`rng`需要先进行定义。

### randn

&emsp;&emsp;`randn`的作用是返回高斯分布的随机数，填入数组或矩阵：

``` cpp
randn ( dst, mean, stddev );
```

参数`dst`是输出数组或矩阵，`mean`是均值，`stddev`是标准差。

``` cpp
Mat_<int> randnM ( 3, 3 );
randn ( randnM, 0, 1 );
cout << "randnM = " << randnM << endl;
```

### randShuffle

&emsp;&emsp;`randShuffle`的作用是将原数组(矩阵)打乱：

``` cpp
randShuffle (InputOutputArray dst, double iterFactor = 1., RNG *rng = 0);
```

- `dst`：输入输出数组(一维)。
- `iterFactor`：决定交换数值的行列的位置的一个系数。
- `rng`：(可选)随机数产生器，它决定了打乱的方法。`0`表示使用默认的随机数产生器，即`seed = -1`。

``` cpp
Mat randShufM = ( Mat_<double> ( 2, 3 ) << 1, 2, 3, 4, 5, 6 );
randShuffle ( randShufM, 7, 0 );
cout << "randShufM = " << endl << randShufM << endl;
```

### CvRNG

&emsp;&emsp;`CvRNG`的作用是产生`64`位随机整数，`C++`版本中的`RNG`已经代替了`CvRNG`。

### cvRandArr

&emsp;&emsp;`cvRandArr`的作用是用`CvRNG`产生的随机数填充数组(矩阵)：

``` cpp
void cvRandArr (CvRNG *rng, CvArr *arr, int dist_type, CvScalar param1, CvScalar param2);
```

- `rng`：被`cvRNG`初始化的`RNG`状态。
- `arr`：输出数组。
- `dist_type`：`CV_RAND_UNI`或者`CV_RAND_NORMAL`。
- `param1`：如果是均匀分布，则它是随机数范围的闭下边界；如果是正态分布，则它是随机数的平均值。
- `param2`：如果是均匀分布，则它是随机数范围的开上边界；如果是正态分布，则它是随机数的标准差。

``` cpp
CvMat *cvM = cvCreateMat ( 3, 3, CV_16U ); /* 创建“3 * 3”的矩阵 */
/* 给cvM赋值，范围是[0, 255) */
cvRandArr ( &cvRNG, cvM, CV_RAND_UNI, cvScalarAll ( 0 ), cvScalarAll ( 255 ) );
cout << "cvM = ";

for ( int i = 0; i < 3; i++ ) {
    for ( int j = 0; j < 3; j++ ) {
        cout << ( int ) cvGetReal2D ( cvM, i, j ) << "   ";
    }

    cout << endl;
}
```

### cvRandInt

&emsp;&emsp;函数原型如下：

``` cpp
unsigned int cvRandInt ( CvRNG *rng );
```

`cvRandInt`返回均匀分布的随机`32 bit`无符号整型值，并更新`RNG`状态；它和`C`运行库里面的`rand`函数十分相似，但是它产生的总是一个`32 bit`数，而`rand`返回一个`0`到`RAND_MAX`(它是`2^16`或者`2^32`，依赖于操作平台)之间的数。

``` cpp
int cvInt = cvRandInt ( &cvRNG );
cout << "cvInt = " << cvInt << endl;
```

### cvRandReal

&emsp;&emsp;函数原型如下：

``` cpp
double cvRandReal ( CvRNG *rng );
```

函数的作用是返回均匀分布的随机浮点数，范围是`[0, 1)`。

``` cpp
double cvDouble = cvRandReal ( &cvRNG );
cout << "cvDouble = " << cvDouble << endl;
```

### 代码示例

&emsp;&emsp;示例代码如下：

``` cpp
#include <opencv2/highgui/highgui.hpp>
#include <opencv2/core/core.hpp>
#include <iostream>
#include "cv.h"
#include "highgui.h"

using namespace cv;
using namespace std;

int main ( int argc, char **argv ) {
    RNG rng;
    int N1 = rng;
    double N1a = rng.uniform ( 0, 1 );
    double N1b = rng.uniform ( ( double ) 0, ( double ) 1 );
    double N1c = rng.uniform ( 0.f, 1.f );
    double N1d = rng.uniform ( 0., 1. );
    double N1g = rng.gaussian ( 2 );

    cout << "N1 = " << N1 << endl;
    cout << "N1a = " << N1a << endl;
    cout << "N1b = " << N1b << endl;
    cout << "N1c = " << N1c << endl;
    cout << "N1d = " << N1d << endl;
    cout << "N1g = " << N1g << endl;

    int N2 = rng.next();
    int N2a = rng.operator uchar();
    int N2b = rng.operator schar();
    int N2c = rng.operator ushort();
    int N2d = rng.operator short int();
    int N2e = rng.operator int();
    int N2f = rng.operator unsigned int();
    int N2g = rng.operator float();
    int N2h = rng.operator double();
    int N2i = rng.operator () (); /* 和“rng.next()”等价 */
    int N2j = rng.operator () ( 100 ); /* 返回[0, 100)范围内的随机数 */

    cout << "N2 = " << N2 << endl;
    cout << "N2a = " << N2a << endl;
    cout << "N2b = " << N2b << endl;
    cout << "N2c = " << N2c << endl;
    cout << "N2d = " << N2d << endl;
    cout << "N2e = " << N2e << endl;
    cout << "N2f = " << N2f << endl;
    cout << "N2g = " << N2g << endl;
    cout << "N2h = " << N2h << endl;
    cout << "N2i = " << N2i << endl;
    cout << "N2j = " << N2j << endl << endl;

    Mat_<int>fillM ( 3, 3 );
    rng.fill ( fillM, RNG::UNIFORM, 1, 1000 );
    cout << "filM = " << fillM << endl << endl;
    Mat fillM1 ( 3, 3, CV_8U );
    rng.fill ( fillM1, RNG::UNIFORM, 1, 1000, TRUE );
    cout << "filM1 = " << fillM1 << endl << endl;
    Mat fillM2 ( 3, 3, CV_8U );
    rng.fill ( fillM2, RNG::UNIFORM, 1, 1000, FALSE );
    cout << "filM2 = " << fillM2 << endl << endl;
    Mat_<double>fillN ( 3, 3 );
    rng.fill ( fillN, RNG::NORMAL, 1, 3 );
    cout << "filN = " << fillN << endl << endl;
    Mat_<int>randuM ( 3, 3 );
    randu ( randuM, Scalar ( 0 ), Scalar ( 255 ) );
    cout << "randuM = " << randuM << endl << endl;
    Mat_<int>randnM ( 3, 3 );
    randn ( randnM, 0, 1 );
    cout << "randnM = " << randnM << endl << endl;
    Mat randShufM = ( Mat_<double> ( 2, 3 ) << 1, 2, 3, 4, 5, 6 );
    randShuffle ( randShufM, 7, 0 );
    cout << "randShufM = " << endl << randShufM << endl << endl;
    CvRNG cvRNG;
    CvMat *cvM = cvCreateMat ( 3, 3, CV_16U );
    cvRandArr ( &cvRNG, cvM, CV_RAND_UNI, cvScalarAll ( 0 ), cvScalarAll ( 255 ) );
    cout << "cvM = ";

    for ( int i = 0; i < 3; i++ ) {
        for ( int j = 0; j < 3; j++ ) {
            cout << ( int ) cvGetReal2D ( cvM, i, j ) << "   ";
        }

        cout << endl;
    }

    cout << endl;
    int cvInt = cvRandInt ( &cvRNG );
    cout << "cvInt = " << cvInt << endl;
    double cvDouble = cvRandReal ( &cvRNG );
    cout << "cvDouble = " << cvDouble << endl;
    printf ( "\nrand1 =" );

    for ( int i = 0; i < 10; i++ ) {
        printf ( "%d ", rand() % 10 );
    }

    printf ( "\nsrand1 = " );
    srand ( 8 );

    for ( int i = 0; i < 10; i++ ) {
        printf ( "%d ", rand() % 10 );
    }

    printf ( "\nsrand2 = " );
    srand ( ( unsigned ) time ( NULL ) );

    for ( int i = 0; i < 10; i++ ) {
        printf ( "%d ", rand() % 10 );
    }

    printf ( "\n" );
    return 0;
}
```

执行结果：

``` cpp
N1 = 130063606
N1a = 0
N1b = 0.901059
N1c = 0.937133
N1d = 0.74879
N1g = 0.610327
N2 = -825516420
N2a = 89
N2b = 31
N2c = 63210
N2d = -12177
N2e = -1825102358
N2f = -1445585521
N2g = 0
N2h = 0
N2i = -1504473379
N2j = 3

filM = [206, 507, 646;
  656, 931, 673;
  416, 656, 907]

filM1 = [198, 192, 197;
  41, 8, 244;
  231, 46, 7]

filM2 = [255, 15, 255;
  255, 77, 255;
  175, 255, 251]

filN = [-1.242041528224945, -0.8259760141372681, 0.361901268362999;
  4.484118342399597, -4.914619922637939, 3.093811929225922;
  0.3505408614873886, 1.294025518000126, 1.788093090057373]

randuM = [91, 2, 79;
  179, 52, 205;
  236, 8, 181]

randnM = [-1, 1, 0;
  1, -2, -2;
  -1, -1, 2]

randShufM =
[4, 2, 3;
 1, 5, 6]

cvM = 126   39   3
136   46   146
241   78   132

cvInt = 526971887
cvDouble = 0.909635

rand1 = 3 6 7 5 3 5 6 2 9 1
srand1 = 6 4 2 9 1 3 2 1 7 3
srand2 = 9 1 1 9 1 7 9 3 7 6
```