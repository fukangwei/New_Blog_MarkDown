---
title: DisplayMetrics类
date: 2018-12-28 11:43:40
categories: Android
---
&emsp;&emsp;`Android`开发经常需要获取手机屏幕的大小，常用的方法就是用`DisplayMetrics`类来获取手机画面宽高：

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.DisplayMetrics;
import android.widget.Toast;
​
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        DisplayMetrics displaysMetrics = new DisplayMetrics();
        getWindowManager().getDefaultDisplay().getMetrics(displaysMetrics);
        String showSize = "手机屏幕分辨率：" + displaysMetrics.widthPixels + " * " + displaysMetrics.heightPixels;
        Toast.makeText(MainActivity.this, showSize, Toast.LENGTH_LONG).show();
    }
}
```