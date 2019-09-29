---
title: Message类
categories: Android
abbrlink: 88e73f03
date: 2018-12-28 13:59:46
---
&emsp;&emsp;`Message`对象可以包含一些简单的数据，并且可以通过`Handler`进行发送和处理。`Message`对象可以通过`Message.what(int)`方法中的`int`参数进行标志。`Message`对象使用两个`int`类型的字段来存储需要发送的信息，也可以使用一个`Object`类型的对象来存储一个简单对象的信息。

- `Message.what`：标识一个`Message`对象。
- `Message.arg1`：需要传递的`int`类型的数据。
- `Message.arg2`：需要传递的`int`类型的数据。
- `Message.obj`：存储任意数据类型(`Object`)的对象。

&emsp;&emsp;你可以使用`Message msg = new Message()`方法来创建一个`Message`对象，但是不建议这么做，因为我们有更高效的方法来获得`Message`对象：使用`Message msg = Message.obtain()`或`Handler.obtainMessage()`来获取一个`Message`。这个`Message`对象被`Handler`发送到`MessageQueue`之后，并不会被销毁，可以重复利用，因此比使用`new`方法来创建一个`Message`对象效率更高。
&emsp;&emsp;当你需要将消息传递到一个后台线程时，建议使用`Handler.obtainMessage`来创建一个`Message`对象：

``` java
int what = 0;
String hello = "Hello!";
/* 获取一个和后台线程关联的Message对象 */
Message msg = mHandler.obtainMessage(what, hello);
/* 发送一个消息到后台线程 */
mHandler.sendMessage(msg);
```

&emsp;&emsp;根据使用场景不同，`Android`系统提供了几种常用的封装：

- `Handler.sendMessage(Message msg)`：在`MessageQueue`中添加一个`Message`对象。
- `Handler.sendMessageAtFrontOfQueue(Message msg)`：添加一个`Message`对象到`MessageQueue`的前面。
- `Handler.sendMessageAtTime(Message msg, long timeInMills)`：在指定的时间发送一个`Message`对象。
- `Handler.sendMessageDelayed(Message msg, long timeInMillis)`：在指定的时间之后，发送`Message`对象。

发送完一个消息后，怎么处理发送的消息任务呢？在我们创建`Handler`对象的时候，可以实现它的`handleMessage`方法，在这个方法里面处理`handler`发送的`Message`对象：

``` java
private Handler mHandler = new Handler() {
    @Override
    public void handleMessage(Message msg) {
        super.handleMessage(msg);
        /* 根据“msg.what”字段的标志处理不同的消息 */
        /* 如果Message比较简单，你也可以不使用“msg.what”来作为区分 */
        switch (msg.what) {
            /* ... */
        }
    }
};
```

当然还有很多处理`Message`消息的方法，比如你想取消一个`Message`的发送，可以调用`handler.removeMessages(int what)`方法，其他的方法可以自行查阅`API`文档。