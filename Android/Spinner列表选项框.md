---
title: Spinner列表选项框
categories: Android
abbrlink: 2b16ab30
date: 2018-12-28 14:14:16
---
&emsp;&emsp;`Spinner`相关属性如下：

- `android:dropDownHorizontalOffset`：设置列表框的水平偏移距离。
- `android:dropDownVerticalOffset`：设置列表框的水平竖直距离。
- `android:dropDownSelector`：列表框被选中时的背景。
- `android:dropDownWidth`：设置下拉列表框的宽度。
- `android:gravity`：设置里面组件的对其方式。
- `android:popupBackground`：设置列表框的背景。
- `android:prompt`：设置对话框模式的列表框的提示信息(标题)，只能够引用`string.xml`中的资源`id`，而不能直接写字符串。
- `android:spinnerMode`：列表框的模式，有两个可选值：`dialog`(对话框风格的窗口)和`dropdown`(下拉菜单风格的窗口(默认))。
- `android:entries`：使用数组资源设置下拉列表框的列表项目。

&emsp;&emsp;`array.xml`如下：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="languages">
        <item>C语言</item>
        <item>java</item>
        <item>php</item>
        <item>xml</item>
        <item>html</item>
    </string-array>
</resources>
```

`activity_main.xml`如下：

``` xml
<LinearLayout xmlns:android="http://schemas.android.com/apk/res/android"
    android:layout_width="match_parent"
    android:layout_height="match_parent"
    android:orientation="vertical">
​
    <Spinner
        android:id="@+id/spinner1"
        android:layout_width="wrap_content"
        android:layout_height="wrap_content"
        android:entries="@array/languages" />
</LinearLayout>
```

`Java`文件如下：

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.Spinner;
import android.widget.Toast;
​
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Spinner spinner = (Spinner) findViewById(R.id.spinner1); /* 初始化控件 */
        String[] mItems = getResources().getStringArray(R.array.languages); /* 建立数据源 */
        /* 建立Adapter并且绑定数据源 */
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_spinner_item, mItems);
        adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
        spinner.setAdapter(adapter); /* 绑定Adapter到控件 */
        spinner.setOnItemSelectedListener(new AdapterView.OnItemSelectedListener() {
            @Override
            public void onItemSelected(AdapterView<?> parent, View view, int position, long id) {
                String[] languages = getResources().getStringArray(R.array.languages);
                Toast.makeText(MainActivity.this, "你点击的是：" + languages[position], Toast.LENGTH_SHORT).show();
            }
​
            @Override
            public void onNothingSelected(AdapterView<?> parent) {
            }
        });
    }
}
```