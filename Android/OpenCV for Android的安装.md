---
title: OpenCV for Android的安装
categories: Android
date: 2019-03-02 11:42:04
---
&emsp;&emsp;首先下载`OpenCV sdk for Android`，并进行解压操作(我的目录是`D:\AndroidSDK`)。然后将`opencv`导入`Android Studio`中，方法是`File -> New -> Import Module`，选择`D:\AndroidSDK\OpenCV-android-sdk\sdk\java`。对于`Module name`，`Android studio`会自动填充这项为`openCVLibrary341`：<!--more-->

<img src="./OpenCV for Android的安装/1.png">

随后点击`Next`，将三个选项框全勾上：

<img src="./OpenCV for Android的安装/2.png">

回到`Android Studio`的菜单栏，点击`File -> Project Structure`，在左边`Modules`栏下选择`app`，在右边上面一栏中选择`Dependencies`，点击右边栏中的`+`，选择`Module Dependency`：

<img src="./OpenCV for Android的安装/3.png" height="262" width="682">

&emsp;&emsp;将视图由`Android`切换到`Project`，点击`app`，打开该目录下的`build.gradle`文件，注意`compileSdkVersion`、`buildToolsVersion`、`minSdkVersion`和`targetSdkVersion`后面的数值，然后用这些值将`openCVLibrary341`中的`build.gradle`对应值进行替换：

<img src="./OpenCV for Android的安装/4.png" height="275" width="548">

把`D:\AndroidSDK\OpenCV-android-sdk\sdk\native`中的`libs`文件夹拷贝到自己的`android`项目下(我的是`TestOpencvAd\app\src\main`)，并且将`libs`改名为`jniLibs`。此时前期准备工作就完成了，接下来可以用`opencv`中的程序进行测试。
&emsp;&emsp;打开主活动`MainActivity.java`文件，并记下包名(我的是`package com.xiaoma.opencvtest;`)。然后将`OpenCV-android-sdk\samples\image-manipulations\res`中的三个文件(`drawable`、`layout`和`values`)拷贝到`android`项目`Opencvtest\app\src\main\res`下(拷贝之前先将此目录下的同名的三个文件删掉，注意保留`res\drawable\ic_launcher_background.xml`文件)。
&emsp;&emsp;将`OpenCV-android-sdk\samples\image-manipulations`中的`AndroidManifest.xml`文件拷贝到`android`项目`Opencvtest\app\src\main`下进行替换。
&emsp;&emsp;将`OpenCV-android-sdk\samples\image-manipulations\src\org\opencv\samples\imagemanipulations`中的`ImageManipulationsActivity.java`文件拷贝到你的`android`项目`Opencvtest\app\src\main\java\com\xiaoma\opencvtest`下，并且把原来的`MainActivity.java`文件删掉。
&emsp;&emsp;在`Android Studio`中打开`ImageManipulationsActivity.java`和`AndroidManifest.xml`，把文件中的包名替换成上面步骤中记下的包名。然后将`AndroidManifest.xml`文件中`uses-sdk android:minSdkVersion`的值改成之前记下的版本号的值。找到`android:theme`行，将其修改为：

``` xml
android:theme="@android:style/Theme.Holo.Light.DarkActionBar"
```

这是因为原版的代码是`fullscreen`状态，导致打开`app`后看不见菜单，这样修改就没问题了。最后对代码进行编译，然后安装`app`至手机上。