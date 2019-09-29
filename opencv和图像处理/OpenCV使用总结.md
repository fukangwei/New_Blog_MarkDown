---
title: OpenCV使用总结
categories: opencv和图像处理
abbrlink: 6f05000e
date: 2018-12-30 14:31:59
---
### saturate_cast的作用

&emsp;&emsp;`saturate_cast`用于防止数据溢出，其大致原理如下：

``` cpp
if ( data < 0 ) {
    data = 0;
} else if ( data > 255 ) {
    data = 255;
}
```

示例代码如下：

``` cpp
for ( int i = 0; i < src1.rows; i++ ) {
    const uchar *src1_ptr = src1.ptr<uchar> ( i );
    const uchar *src2_ptr = src2.ptr<uchar> ( i );
    uchar *dst_ptr  = dst.ptr<uchar> ( i );
​
    for ( int j = 0; j < src1.cols * nChannels; j++ ) {
        /* 加入保护 */
        dst_ptr[j] = saturate_cast<uchar> ( src1_ptr[j] * alpha + src2_ptr[j] * beta + gama );
        // dst_ptr[j] = ( src1_ptr[j] * alpha + src2_ptr[j] * beta + gama ); /* 未加入保护 */
    }
}
```

### opencv矩阵赋值函数

&emsp;&emsp;`opencv`矩阵赋值函数`copyTo`、`clone`以及重载运算符`=`之间实现的功能相似，都是给不同的矩阵赋值功能。
&emsp;&emsp;`copyTo`是深拷贝，但是否申请新的内存空间，取决于`dst`矩阵头中的大小信息是否与`src`一至。若一致，则只深拷贝并不申请新的空间，否则先申请空间后，再进行拷贝。`clone`是完全的深拷贝，在内存中申请新的空间。

``` cpp
Mat A  = Mat::ones ( 4, 5, CV_32F );
Mat B = A.clone(); /* clone是完全的深拷贝，在内存中申请新的空间，与A独立 */
Mat C;
A.copyTo ( C ); /* 此处的C矩阵大小与A大小不一致，则申请新的内存空间，并完成拷贝，等同于clone */
Mat D = A.col ( 1 );
/* 此处D矩阵大小与A.col(0)大小一致，因此不会申请空间，而是直接进行拷贝，相当于把A的第1列赋值给第二列 */
A.col ( 0 ).copyTo ( D );
```

&emsp;&emsp;对于重载运算符`=`，被赋值的矩阵和赋值矩阵之间空间共享，改变任一个矩阵的值，会同时影响到另一个矩阵。当矩阵作为函数的返回值时，其功能和重载运算符`=`相同。赋值运算符会给矩阵空间增加一次计数，所以函数变量返回后，函数内部申请的变量空间并不会被撤销，在主函数中仍可以正常使用传递后的参数。

### CV_IMAGE_ELEM宏

&emsp;&emsp;该宏用于提取像素值：

``` c
CV_IMAGE_ELEM ( image, elemtype, row, col )
```

参数`image`是`IplImage *`型指针；`elemtype`是数据类型，经常为`uchar`；`row`和`col`分别是数据矩阵的行和列。

``` c
#define CV_IMAGE_ELEM( image, elemtype, row, col ) \
    (((elemtype *)((image)->imageData + (image)->widthStep * (row)))[(col)])
```

- 对于单通道的灰度图像，访问像素时使用：

``` c
CV_IMAGE_ELEM ( image, uchar, i, j );
```

- 对于三通道的彩色图像，访问像素时使用：

``` c
CV_IMAGE_ELEM ( image, uchar, i, j * 3 );
CV_IMAGE_ELEM ( image, uchar, i, j * 3 + 1 );
CV_IMAGE_ELEM ( image, uchar, i, j * 3 + 2 );
```

值得注意的是，初学者容易将`i`和`j`写反了，这样就出现了访问出界的错误：`i`的上限是`img->height`，而`j`的上限是`img->width`。