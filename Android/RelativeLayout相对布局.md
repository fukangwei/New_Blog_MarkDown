---
title: RelativeLayout相对布局
date: 2019-08-11 20:29:22
tags:
---
&emsp;&emsp;使用`LinearLayout`有一个问题，就是当界面比较复杂的时候，需要嵌套多层的`LinearLayout`，这样就会降低`UI Render`的效率(渲染速度)。而且如果是`listview`或者`GridView`上的`item`，效率会更低。另外，太多层`LinearLayout`嵌套会占用更多的系统资源，还有可能引发`StackOverflow`。如果使用`RelativeLayout`的话，可能仅仅需要一层就可以完成了，以`父容器或者兄弟组件参考 + margin + padding`就可以设置组件的显示位置。尽量使用`RelativeLayout + LinearLayout`的`weight`属性搭配。
&emsp;&emsp;父容器定位属性示意图如下：

&emsp;&emsp;根据兄弟组件定位：所谓的兄弟组件就是处于同一层次容器的组件：

&emsp;&emsp;图中的组件`1`、`2`就是兄弟组件，而组件`3`与组件`1`或组件`2`并不是兄弟组件，所以组件`3`不能通过组件`1`或`2`来进行定位，例如`layout_toleftof = "组件1"`这样是会报错的！关于兄弟组件定位，最经典例子就是`梅花布局`:

``` xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:id="@+id/RelativeLayout1"
    android:layout_width="match_parent"
    android:layout_height="match_parent" >

    <!-- 这个是在容器中央的 -->
    <ImageView
        android:id="@+id/img1"
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:layout_centerInParent="true"
        android:src="@drawable/pic1" />

    <!-- 在中间图片的左边 -->
    <ImageView
        android:id="@+id/img2"
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:layout_toLeftOf="@id/img1"
        android:layout_centerVertical="true"
        android:src="@drawable/pic2" />

    <!-- 在中间图片的右边 -->
    <ImageView
        android:id="@+id/img3"
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:layout_toRightOf="@id/img1"
        android:layout_centerVertical="true"
        android:src="@drawable/pic3" />

    <!-- 在中间图片的上面 -->
    <ImageView
        android:id="@+id/img4"
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:layout_above="@id/img1"
        android:layout_centerHorizontal="true"
        android:src="@drawable/pic4" />

    <!-- 在中间图片的下面 -->
    <ImageView
        android:id="@+id/img5"
        android:layout_width="80dp"
        android:layout_height="80dp"
        android:layout_below="@id/img1"
        android:layout_centerHorizontal="true"
        android:src="@drawable/pic5" />

</RelativeLayout>
```

### margin与padding的区别

&emsp;&emsp;初学者对于这两个属性可能会有一点混淆，这里区分下：首先margin代表的是偏移，比如“marginleft = "5dp"”表示组件离容器左边缘偏移5dp；而padding代表的则是填充，而填充的对象针对的是组件中的元素，例如TextView中的文字。比如为TextView设置“paddingleft = "5dp"”，则是在组件里的元素的左边填充5dp的空间。margin针对的是容器中的组件，而padding针对的是组件中的元素，要区分开来！下面通过简单的代码演示两者的区别：

``` xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">

    <Button
        android:id="@+id/btn1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="Button" />
​
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_toRightOf="@id/btn1"
        android:paddingLeft="100dp"
        android:text="Button" />
​
    <Button
        android:id="@+id/btn2"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:text="Button" />
​
    <Button
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_marginLeft="100dp"
        android:layout_toRightOf="@id/btn2"
        android:text="Button" />
​
</RelativeLayout>
```

### margin可以设置为负数

&emsp;&emsp;平时我们设置`margin`的时候都习惯了是正数的，其实是可以用负数。我们经常看到，进入软件后弹出广告，广告页面的右上角有一个`cancle`按钮，它的`margin`使用的是负数：

``` xml
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:background="#00CCCCFF" >

    <ImageView
        android:id="@+id/imgBack"
        android:layout_width="200dp"
        android:layout_height="200dp"
        android:layout_centerInParent="true"
        android:background="@drawable/myicon" />

    <ImageView
        android:id="@+id/imgCancle"
        android:layout_width="28dp"
        android:layout_height="28dp"
        android:layout_alignRight="@id/imgBack"
        android:layout_alignTop="@id/imgBack"
        android:background="@drawable/cancel"
        android:layout_marginTop="-15dp"
        android:layout_marginRight="-10dp" />

</RelativeLayout>
```