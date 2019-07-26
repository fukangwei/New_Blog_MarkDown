---
title: Button按钮
date: 2018-12-28 14:37:00
categories: Android
---
&emsp;&emsp;我们可以为按钮建立单击事件监听器，这样就可以处理单击事件。`XML`文件如下：

``` xml
<Button
    android:id="@+id/button_first"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:text="OK" />
```

`Java`文件如下：

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
import android.widget.Toast;
​
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button01 = (Button) findViewById(R.id.button_first);
        button01.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Toast.makeText(MainActivity.this, "单击了按钮", Toast.LENGTH_SHORT).show();
            }
        });
    }
}
```

&emsp;&emsp;实际上也可以通过`onclick`属性来实现：首先在`Activity`中编写一个包含`View`类型参数的方法：

``` java
public myClick(View view){
    /* 编写要执行的代码 */
}
```

再将`android:onClick`属性指定为上述方法名。`XML`文件如下：

``` xml
<Button
    android:id="@+id/button_first"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:onClick="myClick"
    android:text="Button_First"/>
​
<Button
    android:id="@+id/button_second"
    android:layout_width="wrap_content"
    android:layout_height="wrap_content"
    android:onClick="myClick"
    android:text="Button_Second"/>
```

`Java`文件如下：

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Toast;
​
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
​
    public void myClick(View view) {
        switch (view.getId()) {
            case R.id.button_first:
                Toast.makeText(MainActivity.this, "单击了第一个按钮", Toast.LENGTH_SHORT).show();
                break;
            case R.id.button_second:
                Toast.makeText(MainActivity.this, "单击了第二个按钮", Toast.LENGTH_SHORT).show();
                break;
        }
    }
}
```

&emsp;&emsp;`ImageButton`和`Button`属性类似，都可以设置单击处理事件，但`ImageButton`没有属性`android:text`，文字要靠图片来实现。设置图片使用如下语句：

``` xml
android:src="图片路径"
```