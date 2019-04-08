---
title: mixChannels函数
date: 2018-12-30 15:45:32
categories: opencv和图像处理
---
&emsp;&emsp;Copies specified channels from input arrays to the specified channels of output arrays.

``` c
void mixChannels (const Mat *src, int nsrc, Mat *dst, int ndst, const int *fromTo, size_t npairs);
```

- `src`: Input array or vector of matrices. All the matrices must have the same size and the same depth.
- `nsrc`: Number of matrices in `src`.
- `dst`: Output array or vector of matrices. All the matrices must be allocated. Their size and depth must be the same as in `src[0]`.
- `ndst`: Number of matrices in `dst`.
- `fromTo`: Array of index pairs specifying which channels are copied and where. `fromTo[k * 2]` is a `0-based` index of the input channel in `src`. `fromTo[k * 2 + 1]` is an index of the output channel in `dst`. The continuous channel numbering is used: the first input image channels are indexed from `0` to `src[0].channels() - 1`, the second input image channels are indexed from `src[0].channels()` to `src[0].channels() + src[1].channels() - 1`, and so on. The same scheme is used for the output image channels. As a special case, when `fromTo[k * 2]` is negative, the corresponding output channel is filled with `zero`.
- `npairs`: Number of index pairs in fromTo.

&emsp;&emsp;In the example below, the code splits a `4-channel` RGBA image into a `3-channel` BGR (with R and B channels swapped) and a separate `alpha-channel` image:
&emsp;&emsp;example one:

``` cpp
#include "opencv2/highgui/highgui.hpp"
#include "opencv2/imgproc/imgproc.hpp"
#include <iostream>
#include <stdio.h>
​
using namespace std;
using namespace cv;
​
int main ( int argc, char **argv ) {
    Mat rgba ( 3, 4, CV_8UC4, Scalar ( 1, 2, 3, 4 ) );
    Mat bgr ( rgba.rows, rgba.cols, CV_8UC3 );
    Mat alpha ( rgba.rows, rgba.cols, CV_8UC1 );
    /* forming an array of matrices is a quite efficient operation,
       because the matrix data is not copied, only the headers */
    Mat out[] = { bgr, alpha };
    /* rgba[0] -> bgr[2], rgba[1] -> bgr[1],
       rgba[2] -> bgr[0], rgba[3] -> alpha[0] */
    int from_to[] = { 0, 2, 1, 1, 2, 0, 3, 3 };
    mixChannels ( &rgba, 1, out, 2, from_to, 4 );
    cout << "rgba:" << endl << rgba << endl;
    cout << "bgr:" << endl << bgr << endl;
    cout << "alpha:" << endl << alpha << endl;
    return 0;
}
```

result:

``` cpp
rgba:
[1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4;
  1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4;
  1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4]
bgr:
[3, 2, 1, 3, 2, 1, 3, 2, 1, 3, 2, 1;
  3, 2, 1, 3, 2, 1, 3, 2, 1, 3, 2, 1;
  3, 2, 1, 3, 2, 1, 3, 2, 1, 3, 2, 1]
alpha:
[4, 4, 4, 4;
  4, 4, 4, 4;
  4, 4, 4, 4]
```

&emsp;&emsp;example two:

``` cpp
#include "opencv2/highgui/highgui.hpp"
#include "opencv2/imgproc/imgproc.hpp"
#include <iostream>
#include <stdio.h>
​
using namespace std;
using namespace cv;
​
int main ( int argc, char **argv ) {
    Mat rgba ( 3, 4, CV_8UC4, Scalar ( 1, 2, 3, 4 ) );
    Mat bgr ( rgba.rows, rgba.cols, CV_8UC3 );
    Mat alpha ( rgba.rows, rgba.cols, CV_8UC1 );
    Mat a ( rgba.rows, rgba.cols, CV_8UC1 );
    Mat b ( rgba.rows, rgba.cols, CV_8UC1 );
    Mat c ( rgba.rows, rgba.cols, CV_8UC1 );;
    Mat d ( rgba.rows, rgba.cols, CV_8UC1 );
    Mat out[] = { a, b, c, d };
    int from_to[] = {0, 0, 1, 1, 2, 2, 3, 3};
    mixChannels ( &rgba, 1, out, 4, from_to, 4 );
    cout << "rgba: " << endl << rgba << endl;
    cout << "a: " << endl << a << endl;
    cout << "b: " << endl << b << endl;
    cout << "c: " << endl << c << endl;
    cout << "d: " << endl << d << endl;
    return 0;
}
```

result:

``` cpp
rgba:
[1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4;
  1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4;
  1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4, 1, 2, 3, 4]
a:
[1, 1, 1, 1;
  1, 1, 1, 1;
  1, 1, 1, 1]
b:
[2, 2, 2, 2;
  2, 2, 2, 2;
  2, 2, 2, 2]
c:
[3, 3, 3, 3;
  3, 3, 3, 3;
  3, 3, 3, 3]
d:
[4, 4, 4, 4;
  4, 4, 4, 4;
  4, 4, 4, 4]
```