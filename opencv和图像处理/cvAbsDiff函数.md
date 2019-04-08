---
title: cvAbsDiff函数
date: 2019-03-04 15:09:31
tags:
---
&emsp;&emsp;函数原型如下：

``` cpp
void cvAbsDiff ( const CvArr *src1, const CvArr *src2, CvArr *dst );
```

- `src1`: The ﬁrst source array.
- `src2`: The second source array.
- `dst`: The destination array.

The function calculates absolute difference between two arrays. All the arrays must have the same data type and the same size (or `ROI` size).

``` cpp
#include <stdlib.h>
#include <stdio.h>
#include <cv.h>
#include <highgui.h>
​
int main ( int argc, char *argv[] ) {
    IplImage *img1, *img2, *img3;
​
    if ( argc < 3 ) {
        printf ( "Usage: main <image-file-name>\n\7" );
        exit ( 0 );
    }
​
    img1 = cvLoadImage ( argv[1] );
    img2 = cvLoadImage ( argv[2] );
​
    if ( !img1 || !img2 ) {
        printf ( "Could not load image file\n" );
        exit ( 0 );
    }
​
    img3 = cvCreateImage ( cvGetSize ( img1 ), img1->depth, img1->nChannels );
    cvAbsDiff ( img1, img2, img3 );
    cvNamedWindow ( "img1", CV_WINDOW_AUTOSIZE );
    cvNamedWindow ( "img2", CV_WINDOW_AUTOSIZE );
    cvNamedWindow ( "img3", CV_WINDOW_AUTOSIZE );
    cvShowImage ( "img1", img1 );
    cvShowImage ( "img2", img2 );
    cvShowImage ( "img3", img3 );
    cvWaitKey ( 0 );
    cvReleaseImage ( &img1 );
    cvReleaseImage ( &img2 );
    cvReleaseImage ( &img3 );
    return 0;
}
```

<img src="./cvAbsDiff函数/1.png" height="188" width="617">

&emsp;&emsp;`python`代码如下：

``` python
import cv2
​
img1 = cv2.imread('test1.jpg')
img2 = cv2.imread('test1-1.jpg')
img = cv2.absdiff(img1, img2)
cv2.imshow('img1', img1)
cv2.imshow('img2', img2)
cv2.imshow('img原图', img)
​
cv2.waitKey(0)
cv2.destroyAllWindows()
```