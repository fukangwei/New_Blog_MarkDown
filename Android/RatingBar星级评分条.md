---
title: RatingBar星级评分条
categories: Android
abbrlink: 9dc82128
date: 2018-12-28 12:37:59
---
&emsp;&emsp;`RatingBar`和`SeekBar`的类结构是一样的，也是`ProgressBar`的子类，也就是说它同样有用`ProgressBar`的相关属性。
&emsp;&emsp;常用的属性如下：

- `android:isIndicator`：是否用作指示，默认`false`，如果设置为`true`，则用户无法改变。
- `android:numStars`：显示多少个星星，必须为整数。
- `android:rating`：默认评分值，必须为浮点数。
- `android:stepSize`：评分每次增加的值，必须为浮点数。

&emsp;&emsp;`XML`文件如下：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<RelativeLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent">
​
    <!-- 星级评分条 -->
    <RatingBar
        android:id="@+id/ratingBar1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_above="@+id/btn"
        android:layout_marginBottom="60dp"
        android:numStars="5"
        android:rating="0" />

    <!--发表评价-->
    <Button
        android:id="@+id/btn"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:layout_alignParentBottom="true"
        android:layout_alignParentRight="true"
        android:background="#FF5000"
        android:text="发表评价" />
</RelativeLayout>
```

`Java`文件如下：

``` java
import android.app.Activity;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.util.Log;
import android.view.View;
import android.widget.Button;
import android.widget.RatingBar;
import android.widget.Toast;
​
public class MainActivity extends Activity {
    private RatingBar ratingbar; /* 星级评分条 */
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ratingbar = (RatingBar) findViewById(R.id.ratingBar1); /* 获取星级评分条 */
        Button button = (Button) findViewById(R.id.btn); /* 获取“提交”按钮 */
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                int result = ratingbar.getProgress(); /* 获取进度 */
                float rating = ratingbar.getRating(); /* 获取等级 */
                float step = ratingbar.getStepSize(); /* 获取每次最少要改变多少个星级 */
                Log.i("星级评分条", "step=" + step + " result=" + result + " rating=" + rating);
                Toast.makeText(MainActivity.this, "你得到了" + rating + "颗星", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```