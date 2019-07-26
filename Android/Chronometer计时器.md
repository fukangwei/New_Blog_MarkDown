---
title: Chronometer计时器
date: 2018-12-28 12:26:19
categories: Android
---
&emsp;&emsp;`Android`给我们提供了一个计时组件`Chronometer`，该组件继承自`TextView`，因此它会显示一段文本内容。但是`Chronometer`并不显示当前时间，它显示的是从某个起始时间开始，一共过去了多长时间。
&emsp;&emsp;`Chronometer`的用法也很简单，它提供了一个`android:format`属性，用于指定计时器的计时格式。除此之外，`Chronometer`支持下面比较常用的方法：

- `setBase(long base)`：设置计时的起始时间。
- `setFormat(String format)`：设置显示的时间格式。
- `start()`：开始计时。
- `stop()`：结束计时。
- `setOnChronometerTickListener(Chronometer.OnChronometerTickListener listener)`：为计时器绑定事件监听事件，当计时改变时触发改监听器。

&emsp;&emsp;`XML`文件如下：

``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
​
    <Chronometer
        android:id="@+id/chronometer"
        android:layout_width="fill_parent"
        android:layout_height="100dp"
        android:gravity="center"
        android:textSize="40sp" />
</LinearLayout>
```

`Java`文件如下：

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.widget.Chronometer;
import android.os.SystemClock;
​
public class MainActivity extends AppCompatActivity {
    Chronometer ch;  /* 定义计时器 */
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ch = (Chronometer) findViewById(R.id.chronometer); /* 获取计时器组件 */
        ch.setBase(SystemClock.elapsedRealtime()); /* 设置起始时间 */
        ch.setFormat("%s"); /* 设置显示时间格式 */
        ch.start(); /* 开启计时器 */
        /* 添加事件监听器 */
        ch.setOnChronometerTickListener(new Chronometer.OnChronometerTickListener() {
            @Override
            public void onChronometerTick(Chronometer chronometer) {
                /* 判断时间计时达到60秒时 */
                if (SystemClock.elapsedRealtime() - ch.getBase() >= 60000) {
                    ch.stop(); /* 停止计时器 */
                }
            }
        });
    }
}
```