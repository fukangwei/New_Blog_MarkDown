---
title: CheckBox复选框
categories: Android
abbrlink: a687c5b9
date: 2018-12-28 11:50:10
---
&emsp;&emsp;`XML`文件如下：

``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
​
    <CheckBox
        android:id="@+id/first_checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="选项1" />
​
    <CheckBox
        android:id="@+id/secend_checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="选项2" />
​
    <CheckBox
        android:id="@+id/third_checkbox"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:text="选项3" />
</LinearLayout>
```

`Java`文件如下：

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.widget.CheckBox;
import android.widget.CompoundButton;
import android.widget.Toast;
​
public class MainActivity extends AppCompatActivity {
    private CheckBox cb_one;
    private CheckBox cb_two;
    private CheckBox cb_three;
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        cb_one = (CheckBox) findViewById(R.id.first_checkbox);
        cb_two = (CheckBox) findViewById(R.id.secend_checkbox);
        cb_three = (CheckBox) findViewById(R.id.third_checkbox);
        cb_one.setOnCheckedChangeListener(new CompoundButton.OnCheckedChangeListener() {
            @Override
            public void onCheckedChanged(CompoundButton buttonView, boolean isChecked) {
                if (cb_one.isChecked()) {
                    Toast.makeText(MainActivity.this, cb_one.getText(), Toast.LENGTH_SHORT).show();
                }
            }
        });
    }
}
```