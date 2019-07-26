---
title: ListView列表视图
date: 2018-12-28 12:14:22
categories: Android
---
&emsp;&emsp;`array.xml`如下：

``` xml
<?xml version="1.0" encoding="utf-8"?>
<resources>
    <string-array name="languages">
        <item>C语言</item>
        <item>java</item>
        <item>xml</item>
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
    <ListView
        android:id="@+id/listview"
        android:layout_width="match_parent"
        android:layout_height="match_parent"
        android:entries="@array/languages" />
</LinearLayout>
```

`Java`代码如下：

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.AdapterView;
import android.widget.ArrayAdapter;
import android.widget.ListView;
import android.widget.Toast;
​
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        ListView listView = (ListView) findViewById(R.id.listview); /* 初始化控件 */
        String[] mItems = getResources().getStringArray(R.array.languages); /* 建立数据源 */
        /* 建立Adapter并且绑定数据源 */
        ArrayAdapter<String> adapter = new ArrayAdapter<String>(this, android.R.layout.simple_spinner_item, mItems);
        adapter.setDropDownViewResource(android.R.layout.simple_spinner_dropdown_item);
        listView.setAdapter(adapter); /* 绑定Adapter到控件 */
        listView.setOnItemClickListener(new AdapterView.OnItemClickListener() {
            @Override
            public void onItemClick(AdapterView<?> parent, View view, int position, long id) {
                String[] languages = getResources().getStringArray(R.array.languages);
                Toast.makeText(MainActivity.this, "你点击的是：" + languages[position], Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```