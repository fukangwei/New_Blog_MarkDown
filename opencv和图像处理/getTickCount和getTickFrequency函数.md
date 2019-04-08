---
title: getTickCount和getTickFrequency函数
date: 2018-12-30 14:24:20
categories: opencv和图像处理
---
&emsp;&emsp;`GetTickcount`函数返回从操作系统启动到当前所经的计时周期数，`getTickFrequency`函数返回每秒的计时周期数。
&emsp;&emsp;下面的代码返回执行`do something`所耗的时间，单位为`秒`：

``` c
double t = ( double ) getTickCount();
/* do something */
t = ( ( double ) getTickCount() - t ) / getTickFrequency();
```

&emsp;&emsp;代码示例：

``` cpp
#include <iostream>
#include <cv.h>
​
using namespace std;
using namespace cv;
​
int main() {
    double t1 = ( double ) getTickCount();
    cout << "t1 = " << t1 << endl;
    int sum = 0;
​
    for ( int i = 0; i < 10000; i++ ) {
        sum += i;
    }
​
    double t2 = ( double ) getTickCount();
    cout << "t2 = " << t2 << endl;
    double time = ( t2 - t1 ) / getTickFrequency();
    cout << "Time = " << time << endl;
    int64 e1 = getTickCount();
    cout << "e1 = " << e1 << endl;
    int sum1 = 0;
​
    for ( int i = 0; i < 10000; i++ ) {
        sum1 += i;
    }
​
    int64 e2 = getTickCount();
    cout << "e2 = " << e2 << endl;
    double time1 = ( e2 - e1 ) / getTickFrequency();
    cout << "time1 = " << time1 << endl;
    return 0;
}
```

执行结果：

``` c
t1 = 9.83253e+14
t2 = 9.83253e+14
Time = 0.000276486
e1 = 983253219316861
e2 = 983253219387079
time1 = 7.0218e-05
```

---

### 使用OpenCV检测程序效率

&emsp;&emsp;`cv2.getTickCount`函数返回从参考点到这个函数被执行的时钟数。所以当你在一个函数执行前后都调用它的话，你就会得到这个函数的执行时间(时钟数)。
&emsp;&emsp;`cv2.getTickFrequency`返回时钟频率，或者说每秒钟的时钟数。所以你可以按照下面的方式得到一个函数运行了多少秒：

``` python
import cv2
​
img1 = cv2.imread('timg1.jpg')
e1 = cv2.getTickCount()
​
for i in range(5, 49, 2):
    img1 = cv2.medianBlur(img1, i)
​
e2 = cv2.getTickCount()
t = (e2 - e1) / cv2.getTickFrequency()
print(t)
```