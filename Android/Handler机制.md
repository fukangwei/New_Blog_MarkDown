---
title: Handler机制
categories: Android
date: 2018-12-28 11:46:48
---
&emsp;&emsp;`Handler`可以将一个任务切换到`Handler`所在线程中去执行，主要作用是更新`UI`。有时需要在子线程进行耗时的`I/O`操作(读取文件或者访问网络)，当耗时任务完成后，需要在`UI`线程中做一些改变。由于我们不能在子线程中访问`UI`控件(这样会发生异常)，所以可以使用`Handler`机制将更新`UI`操作切换到主线程中执行。<!--more-->

``` java
import android.os.Bundle;
import android.app.Activity;
import android.os.Handler;
import android.os.Message;
import android.view.View;
import android.widget.Button;
import android.widget.TextView;

public class MainActivity extends Activity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        final TextView textView = (TextView) findViewById(R.id.textView);
        Button button = (Button) findViewById(R.id.button);
        final Handler handler = new Handler() {
            @Override
            public void handleMessage(Message msg) {
                super.handleMessage(msg);
                if (msg.what == 0xFF) {
                    textView.setText("变化!");
                }
            }
        };

        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Thread thread = new Thread(new Runnable() { /* 创建新线程 */
                    @Override
                    public void run() {
                        handler.sendEmptyMessage(0xFF); /* 发送空消息 */
                    }
                });

                thread.start();
            }
        });
    }
}
```