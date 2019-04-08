---
title: OpenCV基本数据类型
date: 2019-02-23 16:37:31
tags:
---
&emsp;&emsp;`OpenCV`提供了多种基本数据类型，虽然这些数据类型在`C`语言中不是基本类型，但结构都很简单，可将它们作为原子类型。可以在`/OpenCV/cxcore/include`目录下的`cxtypes.h`文件中查看其详细定义。
&emsp;&emsp;数据类型中最简单的就是`CvPoint`，它是一个包含`integer`类型成员`x`和`y`的简单结构体。`CvPoint`有两个变体类型，即`CvPoint2D32f`和`CvPoint3D32f`。前者同样有两个成员`x`和`y`，但它们是浮点类型；而后者却多了一个浮点类型的成员`z`。
&emsp;&emsp;`CvSize`类型与`CvPoint`非常相似，但它的数据成员是`integer`类型的`width`和`height`。如果希望使用浮点类型，则选用`CvSize`的变体类型`CvSize2D32f`。
&emsp;&emsp;`CvRect`类型派生于`CvPoint`和`CvSize`，它包含`4`个数据成员，即`x`、`y`、`width`和`height`。

### CvScalar

&emsp;&emsp;`C`语言接口中定义为结构体`CvScalar`，`C++`接口中定义为类`Scalar`。`CvScalar`中包含一个可以用来存放`4`个`double`数值的数组，一般用来存放像素值，最多可以存放`4`个通道。

``` cpp
typedef struct CvScalar {
    double val[4];
} CvScalar;
```

赋值函数有如下几种：

``` cpp
inline CvScalar cvScalar ( double val0, double val1 = 0, double val2 = 0, double val3 = 0 );
```

最通用的函数，可初始化`1`至`4`个通道。`cvScalar(255)`用于存放单通道图像中的像素，`cvScalar(255, 255, 255)`用于存放三通道图像中的像素。

``` cpp
inline CvScalar cvRealScalar ( double val0 );
```

该函数只使用第一个通道，即`val[0] = val0`，等同于`cvScalar(val0, 0, 0, 0)`。

``` cpp
inline CvScalar cvScalarAll ( double val0123 );
```

所用通道都用`val0123`进行赋值。
&emsp;&emsp;`CV_RGB`是`OPENCV`中的一个宏，用于创建一个色彩值：

``` cpp
#define CV_RGB(r, g, b)  cvScalar((b), (g), (r), 0)
```

当转换为`cvScalar`时，`rgb`的顺序变为`bgr`，这是因为`opencv`存储`RGB`模式的彩图时，采用的通道顺序是`BGR`。
&emsp;&emsp;各数据类型的内联构造函数被列在下表中：`cvPoint`、`cvSize`、`cvRect`和`cvScalar`。这些结构都十分有用，因为它们不仅使代码更容易编写，而且也更易于阅读。假设要在`(5, 10)`和`(20, 30)`之间画一个白色矩形，只需简单调用：

``` cpp
cvRectangle ( myImg, cvPoint ( 5, 10 ), cvPoint ( 20, 30 ), cvScalar ( 255, 255, 255 ) );
```

&emsp;&emsp;`points`、`size`、`rectangles`和`calar`三元组的结构如下：

结构           | 成员                       | 意义
---------------|---------------------------|-----
`CvPoint`      | `int x, y`                | 图像中的点
`CvPoint2D32f` | `float x, y`              | 二维空间中的点
`CvPoint3D32f` | `float x, y, z`           | 三维空间中的点
`CvSize`       | `int width, height`       | 图像的尺寸
`CvRect`       | `int x, y, width, height` | 图像的部分区域
`CvScalar`     | `double val[4]`           | `RGBA`值

### 矩阵和图像类型

&emsp;&emsp;下图为我们展示了三种图像的类或结构层次结构。使用`OpenCV`时，会频繁遇到`IplImage`数据类型，`IplImage`是我们用来为通常所说的图像进行编码的基本结构。这些图像可能是灰度、彩色、`4`通道的(`RGB + alpha`)，其中每个通道可以包含任意的整数或浮点数。因此，该类型比常见的、易于理解的`3`通道`8`位`RGB`图像更通用。
&emsp;&emsp;`OpenCV`提供了大量实用的图像操作符，包括缩放图像、单通道提取、找出特定通道最大最小值、两个图像求和、对图像进行阈值操作等。
&emsp;&emsp;虽然`OpenCV`是由`C`语言实现的，但它使用的结构体也是遵循面向对象的思想设计的。实际上，`IplImage`由`CvMat`派生，而`CvMat`由`CvArr`派生。

<img src="./OpenCV基本数据类型/1.png">

&emsp;&emsp;在开始探讨图像细节之前，我们需要先了解另一种数据类型`CvMat`，它是`OpenCV`的矩阵结构。虽然`OpenCV`完全由`C`语言实现，但`CvMat`和`IplImage`之间的关系就如同`C++`中的继承关系。实质上，`IplImage`可以被视为从`CvMat`中派生的。因此在试图了解复杂的派生类之前，最好先了解基本的类。第三个类`CvArr`可以被视为一个抽象基类，`CvMat`由它派生。在函数原型中，会经常看到`CvArr`(更准确地说是`CvArr *`)，当它出现时，便可以将`CvMat *`或`IplImage *`传递到程序。

### CvMat矩阵结构

&emsp;&emsp;在开始学习矩阵的相关内容之前，我们需要知道两件事情：第一，在`OpenCV`中没有向量(`vector`)结构。任何时候需要向量，都只需要一个列矩阵(如果需要一个转置或者共轭向量，则需要一个行矩阵)；第二，`OpenCV`矩阵的概念与我们在线性代数课上学习的概念相比，更加抽象，尤其是矩阵的元素，并非只能取简单的数值类型。例如，一个用于新建一个二维矩阵的例程具有以下原型：

``` cpp
cvMat *cvCreateMat ( int rows, int cols, int type );
```

`type`可以是任何预定义类型，预定义类型的结构如下：

``` cpp
CV_<bit_depth> (S|U|F)C<number_of_channels>
```

于是，矩阵的元素可以是`32`位浮点型数据(`CV_32FC1`)，或者是无符号的`8`位三元组的整型数据(`CV_8UC3`)，或者是无数的其他类型的元素。一个`CvMat`的元素不一定就是个单一的数字。在矩阵中可以通过单一(简单)的输入来表示多值，这样我们可以在一个三原色图像上描绘多重色彩通道。对于一个包含`RGB`通道的简单图像，大多数的图像操作将分别应用于每一个通道(除非另有说明)。

``` cpp
typedef struct CvMat { /* CvMat矩阵头 */
    int type; /* 数据类型 */
    int step; /* 每行数据的字节数：元素个数*元素类型的字节长度 */
    int *refcount; /* for internal use only */
    int hdr_refcount;
    union {
        uchar *ptr; /* 指向data数据的第一个元素 */
        short *s;
        int *i;
        float *fl;
        double *db;
    } data; /* 共同体data，里面成员共用一个空间 */
    union {
        int rows; /* 像素的行数 */
        int height; /* 图片的高度 */
    };
    union {
        int cols; /* 像素的列数 */
        int width; /* 图片的宽度 */
    };
} CvMat;
```

### IplImage数据结构

&emsp;&emsp;从本质上讲，它是一个`CvMat`对象，但它还有其他一些成员变量将矩阵解释为图像。这个结构最初被定义为`Intel`图像处理库(`IPL`)的一部分。`IplImage`结构的准确定义如下：

``` cpp
typedef struct _IplImage {
    int nSize;
    int ID;
    int nChannels;
    int alphaChannel;
    int depth;
    char colorModel[4];
    char channelSeq[4];
    int dataOrder;
    int origin;
    int align;
    int width;
    int height;
    struct _IplROI *roi;
    struct _IplImage *maskROI;
    void *imageId;
    struct _IplTileInfo *tileInfo;
    int imageSize;
    char *imageData;
    int widthStep;
    int BorderMode[4];
    int BorderConst[4];
    char *imageDataOrigin;
} IplImage;
```

&emsp;&emsp;`width`和`height`这两个变量很重要，其次是`depth`和`nchannals`。`depth`变量的值取自`ipl.h`中定义的一组数据，但与在矩阵中看到的对应变量不同。因为在图像中，我们往往将深度和通道数分开处理，而在矩阵中，我们往往同时表示它们。可用的深度值如下：

宏              | 图像像素类型
----------------|-----------
`IPL_DEPTH_8U`  | 无符号`8`位整数(`8u`)
`IPL_DEPTH_8S`  | 有符号`8`位整数(`8s`)
`IPL_DEPTH_16S` | 有符号`16`位整数(`16s`)
`IPL_DEPTH_32S` | 有符号`32`位整数(`32s`)
`IPL_DEPTH_32F` | `32`位浮点数单精度(`32f`)
`IPL_DEPTH_64F` | `64`位浮点数双精度(`64f`)

通道数`nChannels`可取的值是`1`、`2`、`3`或`4`。
&emsp;&emsp;随后两个重要成员是`origin`和`dataOrder`。`origin`变量可以有两种取值：`IPL_ORIGIN_TL`或者`IPL_ORIGIN_BL`，分别设置坐标原点的位置于图像的左上角或者左下角。在计算机视觉领域，一个重要的错误来源就是原点位置的定义不统一。具体而言，图像的来源、操作系统、编解码器和存储格式等因素都可以影响图像坐标原点的选取。举例来说，你或许认为自己正在从图像上面的脸部附近取样，但实际上却在图像下方的裙子附近取样。避免此类现象发生的最好办法是在最开始的时候检查一下系统，在所操作的图像块的地方画点东西试试。
&emsp;&emsp;`dataOrder`的取值可以是`IPL_DATA_ORDER_PIXEL`或`IPL_DATA_ORDER_PLANE`，前者指明数据是将像素点不同通道的值交错排在一起(这是常用的交错排列方式)，后者是把所有像素同通道值排在一起，形成通道平面，再把平面排列起来。
&emsp;&emsp;参数`widthStep`与前面讨论过的`CvMat`中的`step`参数类似，包括相邻行的同列点之间的字节数。仅凭变量`width`是不能计算这个值的，因为为了处理过程更高效每行都会用固定的字节数来对齐；因此在第`i`行末和第`i + 1`行开始处可能会有些冗于字节。参数`imageData`包含一个指向第一行图像数据的指针。如果图像中有些独立的平面(如当`dataOrder = IPL_DATA_ORDER_PLANE`)那么把它们作为单独的图像连续摆放，总行数为`height`和`nChannels`的乘积。但通常情况下，它们是交错的，使得行数等于高度，而且每一行都有序地包含交错的通道。
&emsp;&emsp;最后还有一个实用的重要参数 -- 感兴趣的区域(`ROI`)，实际上它是另一个`IPL/IPP`结构`IplROI`的实例。`IplROI`包含`xOffset`、`yOffset`、`height`、`width`和`coi`成员变量，其中`COI`代表`channel of interest`(感兴趣的通道)。`ROI`的思想是：一旦设定`ROI`，通常作用于整幅图像的函数便会只对`ROI`所表示的子图像进行操作。如果`IplImage`变量中设置了`ROI`，则所有的`OpenCV`函数就会使用该`ROI`变量。如果`COI`被设置成非`0`值，则对该图像的操作就只作用于被指定的通道上了。

### Vec3b

&emsp;&emsp;`Vec`类似于`C++`中的`vector`，例如`Vec<uchar, 3>`其实就是定义一个`uchar`类型的数组，长度为`3`。`8U`类型的`RGB`彩色图像可以使用`<Vec3b>`，`3`通道`float`类型的矩阵可以使用`<Vec3f>`。对于`Vec`对象，可以使用`[]`符号如操作数组般读写其元素：

``` cpp
Vec3b color; /* 用color变量描述一种RGB颜色 */
color[0] = 255; /* B通道 */
color[1] = 0; /* G通道 */
color[2] = 0; /* R通道 */
```

`cv::mat`的成员函数`at(int y, int x)`可以用来存取图像中对应坐标为`(x, y)`的元素坐标。但是在使用它时要注意，在编译期必须要已知图像的数据类型，这是因为`cv::mat`可以存放任意数据类型的元素，因此at方法的实现是用模板函数来实现的。假设提前已知一幅图像`img`的数据类型为`unsigned char`型灰度图，要对坐标为`(14, 25)`的像素重新赋值为`25`，则对应操作如下：

``` cpp
srcImage.at<uchar> ( 14, 25 ) = 25;
```

如果要操作的图片`img`是一幅数据类型同样为`unsigned char`的彩色图片，再次要求将坐标`(14, 25)`的像素赋值为`25`。这个操作跟上面的就有点区别了，需要对这个像素三个通道的每个对应元素赋值。`Opencv`中图像三原色在内存中的排列顺序为`B`、`G`和`R`，操作过程如下：

``` cpp
img.at<Vec3b> ( 14, 25 ) [0] = 25; /* B通道 */
img.at<Vec3b> ( 14, 25 ) [1] = 25; /* G通道 */
img.at<Vec3b> ( 14, 25 ) [2] = 25; /* R通道 */
```