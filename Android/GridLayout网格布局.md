---
title: GridLayout网格布局
date: 2019-04-12 13:57:39
tags:
---
&emsp;&emsp;`GridLayout`可以自己设置布局中组件的排列方式；可以自定义网格布局有多少行、多少列；可以直接设置组件位于某行某列；可以设置组件横跨几行或者几列。相关属性总结图如下：

&emsp;&emsp;计算器布局的实现如下：

``` xml
<GridLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/GridLayout1"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:columnCount="4"
    android:orientation="horizontal"
    android:rowCount="6" >
​
    <TextView
        android:layout_columnSpan="4"
        android:layout_gravity="fill"
        android:layout_marginLeft="5dp"
        android:layout_marginRight="5dp"
        android:background="#FFCCCC"
        android:text="0"
        android:textSize="50sp" />
​
    <Button
        android:layout_columnSpan="2"
        android:layout_gravity="fill"
        android:text="回退" />
​
    <Button
        android:layout_columnSpan="2"
        android:layout_gravity="fill"
        android:text="清空" />
​
    <Button android:text="+" />
    <Button android:text="1" />
    <Button android:text="2" />
    <Button android:text="3" />
    <Button android:text="-" />
    <Button android:text="4" />
    <Button android:text="5" />
    <Button android:text="6" />
    <Button android:text="*" />
    <Button android:text="7" />
    <Button android:text="8" />
    <Button android:text="9" />
    <Button android:text="/" />
​
    <Button
        android:layout_width="wrap_content"
        android:text="." />
​
    <Button android:text="0" />
    <Button android:text="=" />
​
</GridLayout>
```

&emsp;&emsp;代码很简单，只是“回退”与“清空”按钮横跨两列，而其他的都是直接添加的，默认每个组件都是占一行一列。另外还有一点要注意，我们通过android:layout_rowSpan与android:layout_columnSpan设置了组件横跨多行或者多列，如果你要让组件填满横越过的行或列的话(就像这个计算机显示数字的部分)，需要添加下面这个属性：

``` xml
android:layout_gravity = "fill"
```