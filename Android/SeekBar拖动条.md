---
title: SeekBar拖动条
date: 2018-12-28 12:51:25
categories: Android
---
&emsp;&emsp;`SeekBar`是`ProgressBar`的子类，因此`ProgressBar`的属性都可以用。它还有`android:thumb`属性，该属性允许我们自定义滑块。
&emsp;&emsp;常用的属性如下：

- `android:max="100"`：滑动条的最大值。
- `android:progress="60"`：滑动条的当前值。
- `android:secondaryProgress="70"`：二级滑动条的进度。
- `android:thumb="@mipmap/sb_icon"`：滑块的`drawable`。

接着要说一下`SeekBar`的事件，对于`SeekBar.OnSeekBarChangeListener`，我们只需重写三个对应的方法：

- `onProgressChanged`：进度发生改变时会触发。
- `onStartTrackingTouch`：按住`SeekBar`时会触发。
- `onStopTrackingTouch`：放开`SeekBar`时触发。

&emsp;&emsp;`XML`文件如下：

``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    xmlns:tools="http://schemas.android.com/tools"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical"
    tools:context=".MainActivity">
​
    <SeekBar
        android:id="@+id/seekbar"
        android:layout_width="match_parent"
        android:layout_height="wrap_content"
        android:max="255"
        android:progress="60" />
</LinearLayout>
```

`Java`文件如下：

``` java
import android.app.Activity;
import android.os.Bundle;
import android.widget.SeekBar;
import android.widget.Toast;
​
public class MainActivity extends Activity {
    private SeekBar sb_normal;
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        sb_normal = (SeekBar) findViewById(R.id.seekbar);
        sb_normal.setOnSeekBarChangeListener(new SeekBar.OnSeekBarChangeListener() {
            @Override
            public void onProgressChanged(SeekBar seekBar, int progress, boolean fromUser) {
                Toast.makeText(MainActivity.this, "进度改变" + progress, Toast.LENGTH_SHORT).show();
            }
​
            @Override
            public void onStartTrackingTouch(SeekBar seekBar) {
                Toast.makeText(MainActivity.this, "触碰SeekBar", Toast.LENGTH_SHORT).show();
            }
​
            @Override
            public void onStopTrackingTouch(SeekBar seekBar) {
                Toast.makeText(MainActivity.this, "放开SeekBar", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```