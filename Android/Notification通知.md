---
title: Notification通知
date: 2018-12-28 14:44:31
categories: Android
---
&emsp;&emsp;`Notification`是一种具有全局效果的通知，可以在系统的通知栏中显示。当`APP`向系统发出通知时，它将先以图标的形式显示在通知栏中。用户可以下拉通知栏查看通知的详细信息。通知栏和抽屉式通知栏均是由系统控制，用户可以随时查看。当系统接收到通知时，可以通过震动、响铃、呼吸灯等多种方式进行提醒。
&emsp;&emsp;通知的目的是告知用户`App`事件。在平时的使用中，通知主要有以下几个作用：

- 显示接收到短消息、及时消息等信息(如微信、短信)。
- 显示客户端的推送消息(如广告、优惠、版本更新、推荐新闻)，常用的第三方`SDK`有阿里云推送。
- 显示正在进行的事物(后台运行的程序，如音乐播放进度、下载进度)。

其中，前两点可以归结为与用户交互，第三点是实时的任务提醒，但不可否认的是，第三点也会与用户交互。

### Notification的基本操作

&emsp;&emsp;`Notification`的基本操作主要有创建、更新、取消这三种。一个`Notification`的必要属性有三项，如果不设置，则在运行时会抛出异常：

- 小图标通过`setSmallIcon`方法设置。
- 标题通过`setContentTitle`方法设置。
- 内容通过`setContentText`方法设置。

除了以上三项，其它均为可选项。虽然如此，但还是应该给`Notification`设置一个`Action`，这样就可以直接跳转到`App`的某个`Activity`、启动一个`Service`或者发送一个`Broadcast`，否则`Notification`仅仅只能起到通知的效果，而不能与用户交互。

### 创建Notification

&emsp;&emsp;`Notification`的创建主要涉及到`Notification.Builder`、`Notification`和`NotificationManager`。

- `Notification.Builer`：使用建造者模式构建`Notification`对象。由于`Notification.Builder`仅支持`Android 4.1`及之后的版本，为了解决兼容性问题，`Google`在`Android Support v4`中加入了`NotificationCompat.Builder`类。对于某些在`Android 4.1`之后才特性，即使`NotificationCompat.Builder`支持该方法，在之前的版本中也不能运行。文中使用的都是`NotificationCompat`。
- `Notification`：通知对应类，保存通知相关的数据。`NotificationManager`向系统发送通知时会用到。
- `NotificationManager`：`NotificationManager`是通知管理类，它是一个系统服务。调用`NotificationManager`的`notify`方法可以向系统发送通知。

&emsp;&emsp;代码如下：

``` java
import android.app.Notification;
import android.app.NotificationManager;
import android.app.PendingIntent;
import android.content.Intent;
import android.os.Bundle;
import android.app.Activity;
import android.view.View;
import android.widget.Button;
​
public class MainActivity extends Activity {
    final int NOTIFYID = 0x00;
​
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = (Button) findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                NotificationManager notificationManager = (NotificationManager) getSystemService(NOTIFICATION_SERVICE);
                Notification.Builder notification = new Notification.Builder(MainActivity.this); /* 创建一个通知对象 */
                notification.setAutoCancel(true); /* 设置通知打开后，自动消失 */
                notification.setSmallIcon(R.mipmap.ic_launcher); /* 设置通知图标 */
                notification.setContentTitle("奖励百万红包"); /* 设置通知内容标题 */
                notification.setContentText("点击查看"); /* 设置通知的内容 */
                notification.setWhen(System.currentTimeMillis()); /* 设置发送时间 */
                notification.setDefaults(Notification.DEFAULT_SOUND | Notification.DEFAULT_VIBRATE); /* 设置通知的声音和振动 */
                /* 创建一个启动页面的Intent */
                Intent intent = new Intent(MainActivity.this, Main3Activity.class);
                PendingIntent pendingIntent = PendingIntent.getActivity(MainActivity.this, 0, intent, 0);
                notification.setContentIntent(pendingIntent); /* 设置通知栏点击跳转 */
                notificationManager.notify(NOTIFYID, notification.build()); /* 发送通知 */
            }
        });
    }
}
```