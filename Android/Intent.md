---
title: Intent
categories: Android
abbrlink: b9c0fe27
date: 2019-02-10 10:20:19
---
&emsp;&emsp;`Android`中提供了`Intent`机制来协助应用间的交互与通讯。`Intent`负责对应用中一次操作的动作以及附加数据进行描述，`Android`则根据此`Intent`的描述，负责找到对应的组件，将`Intent`传递给调用的组件，并完成组件的调用。<!--more-->
&emsp;&emsp;`Intent`不仅可用于应用程序之间，也可用于应用程序内部的`Activity`或`Service`之间的交互。因此`Intent`在这里起着一个媒体中介的作用，专门提供组件互相调用的相关信息，实现调用者与被调用者之间的解耦。`SDK`给出了`Intent`作用的表现形式：

- 通过`startActivity`或`Activity.startActivityForResult`启动一个`Activity`。
- 通过`startService`启动一个服务，或者通过`bindService`和后台服务交互。
- 通过广播方法(比如`sendBroadcast`、`sendOrderedBroadcast`和`sendStickyBroadcast`)发给`broadcast receivers`。

### Intent属性的设置

#### Action

&emsp;&emsp;`SDK`中定义了一些标准的动作，`Action`就是表示了要执行操作的字符串(比如查看或选择)，其对应着`Intent Filter`中的`action`标签`<action />`。常见的`Activity Action Intent`常量如下：

常量名称                | 常量值                                | 意义
-----------------------|---------------------------------------|----
`ACTION_MAIN`          | `android.intent.action.MAIN`          | 应用程序入口
`ACTION_VIEW`          | `android.intent.action.VIEW`          | 显示数据给用户
`ACTION_ATTACH_DATA`   | `android.intent.action.ATTACH_DATA`   | 指明附加信息给其他地方的一些数据
`ACTION_EDIT`          | `android.intent.action.EDIT`          | 显示可编辑的数据
`ACTION_PICK`          | `android.intent.action.PICK`          | 选择数据
`ACTION_CHOOSER`       | `android.intent.action.CHOOSER`       | 显示一个`Activity`选择器
`ACTION_GET_CONTENT`   | `android.intent.action.GET_CONTENT`   | 获得内容
`ACTION_DIAL`          | `android.intent.action.GET_CONTENT`   | 显示打电话面板
`ACITON_CALL`          | `android.intent.action.DIAL`          | 直接打电话
`ACTION_SEND`          | `android.intent.action.SEND`          | 直接发短信
`ACTION_SENDTO`        | `android.intent.action.SENDTO`        | 选择发短信
`ACTION_ANSWER`        | `android.intent.action.ANSWER`        | 应答电话
`ACTION_INSERT`        | `android.intent.action.INSERT`        | 插入数据
`ACTION_DELETE`        | `android.intent.action.DELETE`        | 删除数据
`ACTION_RUN`           | `android.intent.action.RUN`           | 运行数据
`ACTION_SYNC`          | `android.intent.action.SYNC`          | 同步数据
`ACTION_PICK_ACTIVITY` | `android.intent.action.PICK_ACTIVITY` | 选择`Activity`
`ACTION_SEARCH`        | `android.intent.action.SEARCH`        | 搜索
`ACTION_WEB_SEARCH`    | `android.intent.action.WEB_SEARCH`    | `Web`搜索
`ACTION_FACTORY_TEST`  | `android.intent.action.FACTORY_TEST`  | 工厂测试入口点

常见的`BroadcastIntent Action`常量如下：

常量名称                   | 描述
--------------------------|-----
`ACTION_TIME_TICK`        | 系统时间每过一分钟发出的广播
`ACTION_TIME_CHANGED`     | 系统时间通过设置发生了变化
`ACTION_TIMEZONE_CHANGED` | 时区改变
`ACTION_BOOT_COMPLETED`   | 系统启动完毕
`ACTION_PACKAGE_ADDED`    | 新的应用程序`apk`包安装完毕
`ACTION_PACKAGE_CHANGED`  | 现有应用程序`apk`包改变
`ACTION_PACKAGE_REMOVED`  | 现有应用程序`apk`包被删除
`ACTION_UID_REMOVED`      | 用户`id`被删除

#### Data

&emsp;&emsp;此处所说的`Intent`中的`data`指的是`URI`对象和数据的`MIME`类型，其对应着`Intent Filter`中的`data`标签`<data />`。
&emsp;&emsp;一个完整的`URI`由`scheme`、`host`、`port`、`path`组成，格式是`<scheme>://<host>:<port>/<path>`，例如`content://com.example.project:200/folder/subfolder/etc`。`URI`就像一个数据链接，组件可以根据此`URI`获得最终的数据来源。通常将`URI`和`action`结合使用，比如我们将`action`设置为`ACTION_VIEW`，应该提供将要被编辑修改的文档的`URI`。
&emsp;&emsp;当创建了一个`Intent`对象的时候，除了指定`URI`之外，指定数据的`MIME`类型也很重要。例如一个`Activity`能够显示图片，但是不能够播放视频，显示图片的`URI`和播放视频的`URI`可能很类似，为了不让`Android`误将一个含有视频`URI`的`Intent`对象传递给一个只能显示图片的`Activity`，我们需要在该`Activity`的`Intent Filter`中指定MIME类型为图片(例如`<data android:mimeType="image/*" ... />`)，并且还要给`Intent`对象设置对应的图片类型的`MIME`，这样`Android`就会基于`URI`和`MIME`类型将`Intent`传递给符合条件的组件。有个特例，如果`URI`使用的是`content:`协议，那么这就说明`URI`所提供的数据将来自于本地设备，即数据由`ContentProvider`提供，这种情况下`Android`会根据`URI`自动推断出`MIME`类型，此种情况我们无需再自己指定`MIME`类型。
&emsp;&emsp;`Intent`的`Action`和`Data`属性匹配如下：

Action属性    | Data属性                       | 说明
--------------|-------------------------------|------
`ACTION_VIEW` | `content://contacts/people/1` | 显示`id`为`1`的联系人信息
`ACTION_DIAL` | `content://contacts/people/1` | 将`id`为`1`的联系人电话号码显示在拨号界面中
`ACITON_VIEW` | `tel:123`                     | 显示电话为`123`的联系人信息
`ACTION_VIEW` | `http://www.google.com`       | 在浏览器中浏览该网站
`ACTION_VIEW` | `file://sdcard/mymusic.mp3`   | 播放`MP3`
`ACTION_VIEW` | `geo:39.2456,116.3523`        | 显示地图

#### type

&emsp;&emsp;`type`是数据类型，用于显式指定`Intent`的数据类型`MIME`。一般`Intent`的数据类型能够根据数据本身进行判定，但是通过设置这个属性，可以强制采用显式指定的类型而不再进行推导。

#### category

&emsp;&emsp;`category`包含了关于组件如何处理`Intent`的一些其他信息。例如`LAUNCHER_CATEGORY`表示`Intent`的接受者应该在`Launcher`中作为顶级应用出现；而`ALTERNATIVE_CATEGORY`表示当前的`Intent`是一系列的可选动作中的一个，这些动作可以在同一块数据上执行。其他的`category`如下：

Constant              | Meaning
----------------------|--------
`CATEGORY_BROWSABLE`  | The target activity can be safely invoked by the browser to display data referenced by a link. For example, an image or an `e-mail` message.
`CATEGORY_GADGET`     | The activity can be embedded inside of another activity that hosts gadgets.
`CATEGORY_HOME`       | The activity displays the home screen, the first screen the user sees when the device is turned on or when the `HOME` key is pressed.
`CATEGORY_LAUNCHER`   | The activity can be the initial activity of a task and is listed in the `top-level` application launcher.
`CATEGORY_PREFERENCE` | The target activity is a preference panel.

#### component

&emsp;&emsp;`component`(组件)指定`Intent`的目标组件的类名称。通常`Android`会根据`Intent`中包含的其它属性的信息，比如`action`、`data`、`type`、`category`进行查找，最终找到一个与之匹配的目标组件。但是如果`component`这个属性有指定的话，将直接使用它指定的组件，而不再执行上述查找过程。指定了这个属性以后，`Intent`的其它所有属性都是可选的。

#### extras

&emsp;&emsp;`extras`(附加信息)是其它所有附加信息的集合。使用`extras`可以为组件提供扩展信息，比如如果要执行`发送电子邮件`这个动作，可以将电子邮件的标题、正文等保存在`extras`里，传给电子邮件发送组件。有的`Intent`需要靠`URI`携带数据，有的`Intent`是靠`extras`携带数据信息。
&emsp;&emsp;你可以通过调用`Intent`对象的各种重载的`putExtra(key,value)`方法向`Intent`中加入各种`键值对`形式的额外数据，也可以直接创建一个`Bundle`对象，向该`Bundle`对象传入很多`键值对`，然后通过调用`Intent`对象的`putExtras(Bundle)`方法将其这些键值对设置到`Intent`对象中。

### 显式和隐式Intent

&emsp;&emsp;理解`Intent`的关键之一是理解清楚`Intent`的两种基本用法：一种是显式的`Intent`，即在构造`Intent`对象时就指定接收者；另一种是隐式的`Intent`，即`Intent`的发送者在构造`Intent`对象时，并不知道也不关心接收者是谁，有利于降低发送者和接收者之间的耦合。
&emsp;&emsp;对于显式`Intent`，`Android`不需要去做解析，因为目标组件已经很明确；`Android`需要解析的是那些隐式`Intent`，通过解析，将`Intent`映射给可以处理此`Intent`的`Activity`、`IntentReceiver`或`Service`。
&emsp;&emsp;显式的`Intent`：如果`Intent`中明确包含了要启动的组件的完整类名(包名及类名)，那么这个`Intent`就是`explict`(显式的)。使用显式`Intent`最典型的情形是在你自己的`App`中启动一个组件，因为你自己肯定知道自己的要启动的组件的类名。比如，为了响应用户操作通过显式的`Intent`在你的`App`中启动一个`Activity`或启动一个`Service`下载文件。
&emsp;&emsp;隐式的`Intent`：如果`Intent`没有包含要启动的组件的完整类名，那么这个`Intent`就是`implict`(隐式的)。虽然隐式的`Intent`没有指定要启动的组件的类名，但是一般情况下，隐式的`Intent`都要指定需要执行的`action`。通常情况下，隐式的`Intent`只用在当我们想在自己的`App`中通过`Intent`启动另一个`App`组件的时候，让另一个`App`组件接收并处理该`Intent`。例如你想在地图上给用户显示一个位置，但是你的`App`又不支持地图展示，这时你可以将位置信息放入到一个`Intent`中，然后给它指定相应的`action`，通过这样隐式的`Intent`请求其他的地图型的`App`(例如`Google Map`)来在地图中展示一个指定的位置。隐式的`Intent`也体现了`Android`的一种设计哲学：我自己的`App`无需包罗万象所有功能，可以通过与其他`App`组合起来，给用户提供很好的用户体验。而连接自己的`App`与其他`App`的纽带就是隐式`Intent`。
&emsp;&emsp;当创建了一个显式`Intent`去启动`Activity`或`Service`的时候，系统会立即启动`Intent`中所指定的组件。
&emsp;&emsp;当创建了一个隐式`Intent`去使用的时候，`Android`系统会将该隐式`Intent`所包含的信息与设备上其他所有`App`的`manifest`文件中注册组件的`Intent Filters`进行对比过滤，从中找出满足能够接收处理该隐式`Intent`的`App`和对应的组件。如果有多个`App`中的某个组件都符合条件，那么`Android`会弹出一个对话框让用户选择需要启动哪个`App`。

### Intent Filter

&emsp;&emsp;`Intent Filter`即`Intent`过滤器，一个组件可以包含`0`个或多个`Intent Filter`。`Intent Filter`是写在`App`的`manifest`文件中的，其通过设置`action`或`uri`数据类型等指明了组件能够处理接收的`Intent`的类型。如果你给你的`Activity`设置了`Intent Filter`，那么这就使得其他的`App`有可能通过隐式`Intent`启动你的这个`Activity`。反之，如果你的`Activity`不包含任何`Intent Filter`，那么该`Activity`只能通过显式`Intent`启动。由于我们一般不会暴露出我们组件的完整类名，所以这种情况下，其他的`App`基本就不可能通过`Intent`启动我们的`Activity`了(因为它们不知道该`Activity`的完整类名)，只能由我们自己的`App`通过显式`Intent`启动。

---

### 启动第三方程序

&emsp;&emsp;可以使用`ComponentName`启动另一个组件，例如`MainActivity.java`：

``` java
package com.example.littlefool.myapplication;
​
import android.content.ComponentName;
import android.content.Intent;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
​
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                ComponentName componentName = new ComponentName(
                    "com.example.littlefool.myapplication",
                    "com.example.littlefool.myapplication.Main3Activity");
                intent.setComponent(componentName);
                startActivity(intent);
            }
        });
    }
}
```

`Main3Activity.java`如下：

``` java
package com.example.littlefool.myapplication;
​
import android.os.Bundle;
import android.support.v7.app.AppCompatActivity;
import android.view.View;
import android.widget.Button;
​
public class Main3Activity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main3);
    }
}
```

`ComponentName`的第一个参数是包的名称，第二个参数是包中的组件名称。

---

### 返回HOME界面

&emsp;&emsp;代码如下：

``` java
import android.content.Intent;
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
        Button button = findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                intent.setAction(intent.ACTION_MAIN);
                intent.addCategory(intent.CATEGORY_HOME);
                startActivity(intent);
            }
        });
    }
}
```

---

### 隐式Intent调用启动浏览器

&emsp;&emsp;代码如下：

``` java
import android.content.Intent;
import android.net.Uri;
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
        Button button = findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                intent.setAction(intent.ACTION_VIEW);
                intent.setData(Uri.parse("https://www.google.com"));
                startActivity(intent);
            }
        });
    }
}
```

---

### Intent Filter的应用

&emsp;&emsp;`Java`代码如下：

``` java
import android.content.Intent;
import android.support.v7.app.AppCompatActivity;
import android.os.Bundle;
import android.view.View;
import android.widget.Button;
​
public class MainActivity extends AppCompatActivity {
    @Override
    protected void onCreate(Bundle savedInstanceState) {
        super.onCreate(savedInstanceState);
        setContentView(R.layout.activity_main);
        Button button = findViewById(R.id.button_first);
        button.setOnClickListener(new View.OnClickListener() {
            @Override
            public void onClick(View v) {
                Intent intent = new Intent();
                intent.setAction(intent.ACTION_VIEW);
                startActivity(intent);
            }
        });
    }
}
```

`AndroidManifest.xml`增加如下内容：

``` xml
<activity android:name=".Main3Activity">
    <intent-filter>
        <action android:name="android.intent.action.VIEW" />
        <category android:name="android.intent.category.DEFAULT" />
    </intent-filter>
</activity>
```