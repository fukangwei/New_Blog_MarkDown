---
title: LinearLayout线性布局
date: 2019-04-12 14:08:13
tags:
---
&emsp;&emsp;`LinearLayout`如下：

&emsp;&emsp;`weight`(权重)属性详解：

``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/LinearLayout1"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">  

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="fill_parent"
        android:background="#ADFF2F"
        android:layout_weight="1"/>

    <LinearLayout
        android:layout_width="0dp"
        android:layout_height="fill_parent"
        android:background="#DA70D6"
        android:layout_weight="2"/>

</LinearLayout>  
```

要实现`1:1`的效果，只需要分别把两个`LinearLayout`的`weight`改成`1`和`1`就可以了。按比例划分水平方向：将涉及到的`View`的`android:width`属性设置为`0dp`，然后设置为`android weight`属性设置比例即可；竖直方向：只需设`android:height`为`0dp`，然后设置`weight`属性即可。

### 为LinearLayout设置分割线

&emsp;&emsp;很多界面开发中都会设置一些下划线或者分割线，从而使得界面更加整洁美观，比如下面的酷狗音乐的注册页面：

对于这种线，我们通常有两种做法：

- 直接在布局中添加一个`view`，这个`view`的作用仅仅是显示出一条线：

``` xml
<View  
    android:layout_width="match_parent"  
    android:layout_height="1px"  
    android:background="#000000" />  
```

这个是水平方向上的黑线，当然你也可以改成其他颜色或者使用图片。

- 使用LinearLayout的一个divider属性，直接为LinearLayout设置分割线，这里就需要你自己准备一张线的图片了。

android:divider：设置作为分割线的图片。
android:showDividers：设置分割线的位置，有none(无)、beginning(开始)、end(结束)、middle(每两个组件间)。
dividerPadding：设置分割线的Padding。

``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:id="@+id/LinearLayout1"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:divider="@mipmap/divide"
    android:dividerPadding="10dp"
    android:orientation="vertical"
    android:showDividers="middle">
​
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="按钮1" />
​
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="按钮2" />
​
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="按钮3" />
​
</LinearLayout>
```