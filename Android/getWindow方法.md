---
title: getWindow方法
categories: Android
abbrlink: edc30032
date: 2018-12-28 11:12:26
---
&emsp;&emsp;设置窗体全屏：

``` java
getWindow().setFlags(
    WindowManager.LayoutParams.FLAG_FULLSCREEN,
    WindowManager.LayoutParams.FLAG_FULLSCREEN
);
```

&emsp;&emsp;设置窗体始终点亮：

``` java
getWindow().setFlags(
    WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON,
    WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON
);
```

取消窗体始终点亮：

``` java
getWindow().setFlags(0, WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
```

增加窗体常亮的特性：

``` java
getWindow().addFlags(WindowManager.LayoutParams.FLAG_KEEP_SCREEN_ON);
```

&emsp;&emsp;设置窗体背景模糊：

``` java
getWindow().setFlags(
    WindowManager.LayoutParams.FLAG_BLUR_BEHIND,
    WindowManager.LayoutParams.FLAG_BLUR_BEHIND
);
```