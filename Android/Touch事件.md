---
title: Touch事件
date: 2018-12-28 15:01:05
categories: Android
---
&emsp;&emsp;重写`Touch`事件回调函数如下：

``` java
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.MotionEvent;
import android.widget.Toast;
​
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
    }
​
    @Override
    public boolean onTouchEvent(MotionEvent event) {
        Toast.makeText(MainActivity.this, "触摸", Toast.LENGTH_SHORT).show();
        return super.onTouchEvent(event);
    }
}
```

&emsp;&emsp;`OnTouchListener`事件监听器如下：

``` java
import android.os.Bundle;
import android.view.MotionEvent;
import android.view.View;
import android.widget.ImageView;
import android.app.Activity;
import android.widget.TextView;
​
public class MainActivity extends Activity {
    private TextView textView = null;
    private ImageView imageView = null;
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        textView = (TextView) findViewById(R.id.info);
        imageView = (ImageView) findViewById(R.id.imageView1);
​
        imageView.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                /* event.getX获取X坐标，event.getY获取Y坐标 */
                String info = "X = " + event.getX() + " Y = " + event.getY();
                textView.setText(info);
                return true; /* 如果设为false，则滑动屏幕时坐标不改变 */
            }
        });
    }
}
```

### 单击事件与触摸事件的区别

&emsp;&emsp;`Android`首先会执行触摸事件，如果`OnTouch`事件监听器返回`ture`，意味着触摸事件监听器做完了所有的处理，则不触发单击事件；返回`false`意味着触摸事件监听器没有做完所有的处理，则触发单击事件，交给单击事件进行处理。
&emsp;&emsp;单击事件触发一个动作，触发事件触发两个动作。

``` java
import android.os.Bundle;
import android.util.Log;
import android.view.MotionEvent;
import android.view.View;
import android.widget.Button;
import android.app.Activity;
​
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button_first);
        /* 为按钮注册单击事件监听器 */
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Log.i("onClick", "单击事件");
            }
        });
​
        /* 为按钮注册触摸事件监听器 */
        button.setOnTouchListener(new View.OnTouchListener() {
            @Override
            public boolean onTouch(View v, MotionEvent event) {
                if (event.getAction() == MotionEvent.ACTION_DOWN) {
                    Log.i("onTouch", "按下");
                } else if (event.getAction() == MotionEvent.ACTION_UP) {
                    Log.i("onTouch", "抬起");
                }
                return true;
            }
        });
    }
}
```

### 处理长按事件

&emsp;&emsp;代码如下：

``` java
import android.os.Bundle;
import android.view.ContextMenu;
import android.view.View;
import android.widget.ImageView;
import android.app.Activity;
​
public class MainActivity extends Activity {
    @Override
    /* 重写onCreateContextMenu菜单，为菜单添加选项值 */
    public void onCreateContextMenu(ContextMenu menu, View v, ContextMenu.ContextMenuInfo menuInfo) {
        super.onCreateContextMenu(menu, v, menuInfo);
        menu.add("收藏");
        menu.add("举报");
    }
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        /* 将长按事件注册到菜单中，并打开菜单 */
        ImageView imageView = (ImageView) findViewById(R.id.imageView1);
        imageView.setOnLongClickListener(new View.OnLongClickListener() {
            @Override
            public boolean onLongClick(View v) {
                registerForContextMenu(v);
                openContextMenu(v);
                return false;
            }
        });
    }
}
```