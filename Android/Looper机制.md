---
title: Looper机制
date: 2018-12-28 14:28:00
categories: Android
---
&emsp;&emsp;`Handler`必须与一个`Looper`关联才能使用。怎么样关联呢？你可以手动传入一个`Looper`对象，让`Handler`关联你传入的`Looper`；也可以什么都不传，这时候`Handler`会自己去找当前线程的`Looper`，如果找到就万事大吉，如果当前线程没有`Looper`，那么就会报错：

``` java
/* 不传入Looper，系统会自己去获取当前线程中的Looper */
Handler handler = new Handler();
/* 传入自定的Looper */
Handler handler = new Handler(Looper);
```

由于主线程(`UI`线程)是唯一一个默认自带了`Looper`的线程，所以在主线程中你可以直接用上面第一种方式创建一个`Handler`对象而不用担心`Looper`的存在性问题。
&emsp;&emsp;在创建`Looper`的时候，系统会检查该线程是否已经有`Looper`对象了，如果已经有`Looper`对象了，你再塞一个进去，它就会报错，因此一个线程中最多只能有一个`Looper`。所以在主线程中不能自己创建一个`Looper`，但是你可以传入当前线程的`Looper`进去：

``` java
/* 在主线程中，你还可以这么创建 Handler，虽然有点多余 */
/* 获取主线程的Looper */
Handler handler = new Handler(getMainLooper());
/* 获取当前线程的Looper */
Handler handler = new Handler(Looper.myLooper());
```

### 在子线程中创建Handler

&emsp;&emsp;一般情况下，我们都是在主线程中创建`Handler`，但是有时也需要在子线程中处理消息队列。在子线程中使用`Handler`需要提供一个`Looper`：

``` java
class LooperThread extends Thread { /* 为异步消息处理框架准备一个线程 */
    public Handler mHandler;
    public void run() {
        Looper.prepare(); /* 准备一个Looper */
        mHandler = new Handler() { /* Handler对象会和该线程的Looper关联起来 */
            public void handlerMessage(Message msg) {
                /* 在这里处理传入的消息 */
            }
        };

        Looper.loop(); /* 使用该Looper，启动一个循环的消息队列 */
    }
}
```

&emsp;&emsp;`MainActivity.java`如下：

``` java
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
        Button button = (Button) findViewById(R.id.button);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                LoopThread loop_thread = new LoopThread();
                loop_thread.start();
            }
        });
    }
}
```

&emsp;&emsp;`LoopThread.java`如下：

``` java
import android.os.Handler;
import android.os.Looper;
import android.os.Message;
import android.util.Log;
​
public class LoopThread extends Thread {
    public Handler handler;
​
    @Override
    public void run() {
        super.run();
        Looper.prepare();
        handler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                super.handleMessage(msg);
                Log.i("Lopper", String.valueOf(msg.what));
            }
        };

        Message message = handler.obtainMessage();
        message.what = 12;
        handler.sendMessage(message);
        Looper.loop();
    }
}
```