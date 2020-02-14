---
title: ImageSwitcher图片切换
categories: Android
abbrlink: 2a456f10
date: 2018-12-28 12:46:29
---
&emsp;&emsp;`XML`文件如下：<!--more-->

``` xml
<?xml version="1.0" encoding="utf-8"?>
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="horizontal">

    <ImageSwitcher
        android:id="@+id/imageSwitcher1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_centerHorizontal="true"
        android:layout_centerVertical="true" >
    </ImageSwitcher>
</LinearLayout>
```

`Java`文件如下：

``` java
import android.app.Activity;
import android.os.Bundle;
import android.view.View;
import android.view.animation.AnimationUtils;
import android.widget.ImageSwitcher;
import android.widget.ImageView;
import android.widget.ViewSwitcher;
​
public class MainActivity extends Activity {
    int count = 0;
    private int[] arrayPictures = new int[]{ /* 声明并初始化一个显示图像ID的数组 */
        R.mipmap.aloel,
        R.mipmap.apple
    };
​
    private ImageSwitcher imageSwitcher; /* 声明一个图像切换器对象 */
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
​
        imageSwitcher = (ImageSwitcher) findViewById(R.id.imageSwitcher1); /* 获取图像切换器 */
        /* 设置图片的切入方式 */
        imageSwitcher.setInAnimation(AnimationUtils.loadAnimation(this, android.R.anim.slide_in_left));
        /* 设置图片的切出方式 */
        imageSwitcher.setOutAnimation(AnimationUtils.loadAnimation(this, android.R.anim.slide_out_right));
        imageSwitcher.setFactory(new ViewSwitcher.ViewFactory() {
            @Override
            public View makeView() {
                ImageView imageView = new ImageView(MainActivity.this); /* 实例化一个ImageView类的对象 */
                imageView.setImageResource(R.mipmap.aloel); /* 根据id加载默认显示图片 */
                return imageView; /* 返回imageView对象 */
            }
        });

        imageSwitcher.setOnClickListener(new View.OnClickListener() { /* 为点击屏幕添加事件监听器 */
            @Override
            public void onClick(View v) {
                imageSwitcher.setImageResource(arrayPictures[++count % arrayPictures.length]);
            }
        });
    }
}
```