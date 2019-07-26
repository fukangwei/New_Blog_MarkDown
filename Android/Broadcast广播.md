---
title: Broadcast广播
date: 2018-12-28 15:10:40
categories: Android
---
&emsp;&emsp;在`Android`系统中，广播(`Broadcast`)是在组件之间传播数据的一种机制，这些组件可以位于不同的进程中，起到进程间通信的作用。
&emsp;&emsp;`BroadcastReceiver`是对发送出来的`Broadcast`进行过滤、接受和响应的组件。首先将要发送的消息和用于过滤的信息(`Action`或`Category`)装入一个`Intent`对象，然后通过调用`sendBroadcast`、`sendOrderBroadcast`方法把`Intent`对象以广播形式发送出去。广播发送出去后，所有已注册的`BroadcastReceiver`会检查注册时的`IntentFilter`是否与发送的`Intent`相匹配，若匹配则会调用`BroadcastReceiver`的`onReceiver`方法。`BroadcastReceiver`的生命周期很短，在执行`onReceiver`方法时才有效，一旦执行完毕，该`Receiver`的生命周期就结束了。
&emsp;&emsp;`Android`中的广播分为两种类型，即标准广播和有序广播：

- `标准广播`：标准广播是一种完全异步执行的广播，在广播发出后，所有的广播接收器会在同一时间接收到这条广播，之间没有先后顺序，效率比较高，且无法被截断。
- `有序广播`：有序广播是一种同步执行的广播，在广播发出之后，同一时刻只有一个广播接收器能够接收到，优先级高的广播接收器会优先接收。当优先级高的广播接收器的`onReceiver`方法运行结束后，广播才会继续传递。并且前面的广播接收器可以选择截断广播，这样后面的广播接收器就无法接收到这条广播了。

### 静态注册

&emsp;&emsp;静态注册即在清单文件中为`BroadcastReceiver`进行注册，使用`<receiver>`标签声明，并在标签内用`<intent-filter>`标签设置过滤器。这种形式的`BroadcastReceiver`的生命周期伴随着整个应用，如果这种方式处理的是系统广播，那么不管应用是否在运行，该广播接收器都能接收到该广播。

#### 发送标准广播

&emsp;&emsp;发送广播的文件为`MainActivity.java`：

``` java
import android.content.Intent;
import android.os.Bundle;
import android.app.Activity;
import android.view.View;
import android.widget.Button;
​
public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                intent.setAction("mayun"); /* 为Intent添加动作“mayun” */
                sendBroadcast(intent); /* 发送广播 */
            }
        });
    }
}
```

接收广播的文件为`broadreceiver.java`：

``` java
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.widget.Toast;
​
public class broadreceiver extends BroadcastReceiver {
    private static final String ACTION1 = "zhakeboge";
    private static final String ACTION2 = "mayun";
​
    @Override
    public void onReceive(Context context, Intent intent) {
        if (intent.getAction().equals(ACTION1)) {
            Toast.makeText(context, "收到扎克伯格的广播", Toast.LENGTH_SHORT).show();
        } else if (intent.getAction().equals(ACTION2)) {
            Toast.makeText(context, "收到马云的广播", Toast.LENGTH_SHORT).show();
        }
    }
}
```

&emsp;&emsp;注册`BroadcastReceiver`：

``` xml
<receiver android:name=".broadreceiver">
    <intent-filter>
        <action android:name="zhakeboge" />
        <action android:name="mayun" />
    </intent-filter>
</receiver>
```

### 动态注册

&emsp;&emsp;动态注册`BroadcastReceiver`是在代码中定义并设置好一个`IntentFilter`对象，然后在需要注册的地方调用`Context.registerReceiver`方法，取消注册则调用`Context.unregisterReceiver`方法，此时就不需要在清单文件中注册`Receiver`了：

``` java
import android.content.BroadcastReceiver;
import android.content.Context;
import android.content.Intent;
import android.content.IntentFilter;
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
        Button button = (Button) findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                intent.setAction("mayun"); /* 为Intent添加动作“mayun” */
                sendBroadcast(intent); /* 发送广播 */
            }
        });
    }
​
    protected void onStart() {
        super.onStart();
        IntentFilter dynamic_filter = new IntentFilter();
        dynamic_filter.addAction("mayun"); /* 添加动态广播的Action */
        registerReceiver(broadreceiver, dynamic_filter); /* 注册自定义动态广播消息 */
    }
​
    protected void onDestroy() {
        super.onDestroy();
        unregisterReceiver(broadreceiver);
        Toast.makeText(MainActivity.this, "BroadcastService取消注册接收器", Toast.LENGTH_SHORT).show();
    }
​
    private BroadcastReceiver broadreceiver = new BroadcastReceiver() {
        private static final String ACTION1 = "zhakeboge";
        private static final String ACTION2 = "mayun";
​
        @Override
        public void onReceive(Context context, Intent intent) {
            if (intent.getAction().equals(ACTION1)) {
                Toast.makeText(MainActivity.this, "收到扎克伯格的广播", Toast.LENGTH_SHORT).show();
            } else if (intent.getAction().equals(ACTION2)) {
                Toast.makeText(MainActivity.this, "收到马云的广播", Toast.LENGTH_SHORT).show();
            }
        }
    };
}
```