---
title: adb使用教程
date: 2019-08-05 15:11:58
categories: Android
---
&emsp;&emsp;`adb`工具位于`SDK`的`platform-tools`目录下，因此需要将`platform-tools`的路径添加到系统环境变量中。常用的`adb`命令如下：

- `adb shell`：可以使用`shell`命令。
- `adb reboot`：重新启动。
- `adb version`：查看`adb`的版本。
- `adb help`：查看`adb`的用法。
<!--more-->
### 命令语法

&emsp;&emsp;`adb`命令的基本语法如下：

``` bash
adb [-d|-e|-s <serialNumber>] <command>
```

如果只有一个设备或模拟器连接时，可以省略掉`[-d|-e|-s <serialNumber>]`这一部分，直接使用`adb <command>`；如果有多个设备或模拟器连接，则需要为命令指定目标设备。

参数                | 含义
--------------------|----
`-d`                | 指定当前唯一通过`USB`连接的`Android`设备为命令目标
`-e`                | 指定当前唯一运行的模拟器为命令目标
`-s <serialNumber>` | 指定相应`serialNumber`号的设备/模拟器为命令目标

在多个设备/模拟器连接的情况下较常用的是`-s <serialNumber>`参数，`serialNumber`可以通过`adb devices`命令获取：

``` bash
$ adb devices
List of devices attached
cf264b8f    device
emulator-5554   device
10.129.164.6:5555   device
```

输出里的`cf264b8f`、`emulator-5554`和`10.129.164.6:5555`即为`serialNumber`。如果指定`cf264b8f`这个设备来运行`adb`命令获取屏幕分辨率，可以使用如下命令：

``` bash
adb -s cf264b8f shell wm size
```

又例如想给`10.129.164.6:5555`这个设备安装应用(这种形式的`serialNumber`格式为`<IP>:<Port>`，一般为无线连接的设备或`Genymotion`等第三方`Android`模拟器)：

``` bash
adb -s 10.129.164.6:5555 install test.apk
```

### USB连接

&emsp;&emsp;通过`USB`连接来正常使用`adb`需要保证几点：

1. 硬件状态正常：包括`Android`设备处于正常开机状态，`USB`连接线和各种接口完好。
2. `Android`设备的开发者选项和`USB`调试模式已开启：可以到`设置 -> 开发者选项 -> Android调试`查看。如果在设置里找不到`开发者选项`，那需要通过一个彩蛋来让它显示出来：在`设置 -> 关于手机`中，连续点击`7`次`版本号`。
3. 设备驱动状态正常。
4. 通过`USB`线连接好电脑和设备后，使用如下命令确认状态：

``` bash
adb devices
```

如果能看到如下命令，则说明连接成功：

``` bash
xxxxxx device
```

### 应用管理

#### 查看应用列表

&emsp;&emsp;查看应用列表的基本命令格式：

``` bash
adb shell pm list packages [-f] [-d] [-e] [-s] [-3] [-i] [-u] [--user USER_ID] [FILTER]
```

在`adb shell pm list packages`的基础上，可以加一些参数进行过滤，支持的过滤参数如下：

参数       | 显示列表
-----------|----------
无         | 所有应用
`-f`       | 显示应用关联的`apk`文件
`-d`       | 只显示`disabled`的应用
`-e`       | 只显示`enabled`的应用
`-s`       | 只显示系统应用
`-3`       | 只显示第三方应用
`-i`       | 显示应用的`installer`
`-u`       | 包含已卸载应用
`<FILTER>` | 包名包含`<FILTER>`字符串

&emsp;&emsp;查看所有应用使用如下命令：

``` bash
adb shell pm list packages
```

输出示例：

``` bash
package:com.android.smoketest
package:com.example.android.livecubes
package:com.android.providers.telephony
package:com.google.android.googlequicksearchbox
package:com.android.providers.calendar
package:com.android.providers.media
package:com.android.protips
package:com.android.documentsui
package:com.android.gallery
package:com.android.externalstorage
// other packages
```

&emsp;&emsp;查看系统应用使用如下命令：

``` bash
adb shell pm list packages -s
```

&emsp;&emsp;查看第三方应用使用如下命令：

``` bash
adb shell pm list packages -3
```

&emsp;&emsp;查看包名包含字符串`mazhuang`的应用列表，可以使用如下命令：

``` bash
adb shell pm list packages mazhuang
```

当然也可以使用`grep`来过滤：

``` bash
adb shell pm list packages | grep mazhuang
```

#### 安装APK

&emsp;&emsp;命令格式如下：

``` bash
adb install [-lrtsdg] <path_to_apk>
```

`adb install`后面可以跟一些可选参数来控制安装`APK`的行为，可用参数及含义如下：

参数 | 含义
-----|----
`-l` | 将应用安装到保护目录`/mnt/asec`
`-r` | 允许覆盖安装
`-t` | 允许安装`AndroidManifest.xml`里`application`指定`android:testOnly="true"`的应用
`-s` | 将应用安装到`sdcard`
`-d` | 允许降级覆盖安装
`-g` | 授予所有运行时权限

运行命令后，如果见到类似如下输出(状态为`Success`)，代表安装成功：

``` bash
[100%] /data/local/tmp/1.apk
    pkg: /data/local/tmp/1.apk
Success
```

而如果状态为`Failure`，则表示安装失败。
&emsp;&emsp;`adb install`实际是分三步完成：

1. `push apk`文件到`/data/local/tmp`。
2. 调用`pm install`安装`apk`文件。
3. 删除`/data/local/tmp`下的对应`apk`文件。

必要时也可以根据这个步骤，分步执行安装过程。

#### 卸载应用

&emsp;&emsp;使用如下命令：

``` bash
adb uninstall [-k] <packagename>
```

`<packagename>`表示应用的包名，`-k`参数可选，表示卸载应用但保留数据和缓存目录。命令示例如下：

``` bash
adb uninstall com.qihoo360.mobilesafe
```

#### 清除应用数据与缓存

&emsp;&emsp;使用如下命令：

``` bash
adb shell pm clear <packagename>
```

`<packagename>`表示应用名包，这条命令的效果相当于在设置里的应用信息界面点击了`清除缓存`和`清除数据`。命令示例如下：

``` bash
adb shell pm clear com.qihoo360.mobilesafe
```

#### 查看前台Activity

&emsp;&emsp;使用如下命令：

``` bash
adb shell dumpsys activity activities | grep mFocusedActivity
```

输出示例：

``` bash
mFocusedActivity: ActivityRecord{8079d7e u0 com.cyanogenmod.trebuchet/com.android.launcher3.Launcher t42}
```

其中的`com.cyanogenmod.trebuchet/com.android.launcher3.Launcher`就是当前处于前台的`Activity`。

#### 查看正在运行的Services

&emsp;&emsp;使用如下命令：

``` bash
adb shell dumpsys activity services [<packagename>]
```

`<packagename>`参数不是必须的，指定`<packagename>`表示查看与其相关的`Services`，不指定`<packagename>`表示查看所有`Services`。
&emsp;&emsp;`<packagename>`不一定要给出完整的包名，比如运行`adb shell dumpsys activity services org.mazhuang`，那么包名`org.mazhuang.demo1`、`org.mazhuang.demo2`和`org.mazhuang123`等相关的`Services`都会列出来。

#### 查看应用详细信息

&emsp;&emsp;使用如下命令：

``` bash
adb shell dumpsys package <packagename>
```

输出中包含很多信息，包括`Activity Resolver Table`、`Registered ContentProviders`、包名、`userId`、安装后的文件资源代码等路径、版本信息、权限信息和授予状态、签名版本信息等。

#### 查看应用安装路径

&emsp;&emsp;使用如下命令：

``` bash
adb shell pm path <packagename>
```

输出示例：

``` bash
adb shell pm path ecarx.weather
package:/data/app/ecarx.weather-1.apk
```

### 与应用交互

&emsp;&emsp;主要是使用`am <command>`命令，常用的`<command>`如下：

command                           | 用途
----------------------------------|----
`start [options] <INTENT>`        | 启动`<INTENT>`指定的`Activity`
`startservice [options] <INTENT>` | 启动`<INTENT>`指定的`Service`
`broadcast [options] <INTENT>`    | 发送`<INTENT>`指定的广播
`force-stop <packagename>`        | 停止`<packagename>`相关的进程

`<INTENT>`参数很灵活，和编写`Android`程序中的`Intent`相对应。用于决定`Intent`对象的选项如下：

参数             | 含义
-----------------|----
`-a <ACTION>`    | 指定`action`，比如`android.intent.action.VIEW`
`-c <CATEGORY>`  | 指定`category`，比如`android.intent.category.APP_CONTACTS`
`-n <COMPONENT>` | 指定完整`component`名，用于明确指定启动哪个`Activity`，如`com.example.app/.ExampleActivity`

`<INTENT>`里还能带数据，就像写代码时的`Bundle`一样：

参数                                                           | 含义
---------------------------------------------------------------|-----
`--esn <EXTRA_KEY>`                                            | `null`值(只有`key`名)
`--ez <EXTRA_KEY> <EXTRA_BOOLEAN_VALUE>`                       | `boolean`值
`--ei <EXTRA_KEY> <EXTRA_INT_VALUE>`                           | `integer`值
`--el <EXTRA_KEY> <EXTRA_LONG_VALUE>`                          | `long`值
`--ef <EXTRA_KEY> <EXTRA_FLOAT_VALUE>`                         | `float`值
`--eu <EXTRA_KEY> <EXTRA_URI_VALUE>`                           | `URI`
`--ecn <EXTRA_KEY> <EXTRA_COMPONENT_NAME_VALUE>`               | `component name`
`--eia <EXTRA_KEY> <EXTRA_INT_VALUE>[, <EXTRA_INT_VALUE...]`   | `integer`数组
`--ela <EXTRA_KEY> <EXTRA_LONG_VALUE>[, <EXTRA_LONG_VALUE...]` | `long`数组

#### 启动应用/调起Activity

&emsp;&emsp;命令格式：

``` bash
adb shell am start [options] <INTENT>
```

如下命令表示调起微信主界面：

``` bash
adb shell am start -n com.tencent.mm/.ui.LauncherUI
```

如下命令表示调起`org.mazhuang.boottimemeasure/.MainActivity`，并传给它`string`数据键值对`toast - hello, world`：

``` bash
adb shell am start -n org.mazhuang.boottimemeasure/.MainActivity --es "toast" "hello, world"
```

#### 调起Service

&emsp;&emsp;命令格式：

``` bash
adb shell am startservice [options] <INTENT>
```

如下命令表示调起微信的某`Service`：

``` bash
adb shell am startservice -n com.tencent.mm/.plugin.accountsync.model.AccountAuthenticatorService
```

另外一个典型的用例是，如果设备上原本应该显示虚拟按键但是没有显示，可以试试如下命令：

``` bash
adb shell am startservice -n com.android.systemui/.SystemUIService
```

#### 停止Service

&emsp;&emsp;命令格式：

``` bash
adb shell am stopservice [options] <INTENT>
```

#### 发送广播

&emsp;&emsp;命令格式：

``` bash
adb shell am broadcast [options] <INTENT>
```

可以向所有组件广播，也可以只向指定组件广播。向所有组件广播`BOOT_COMPLETED`：

``` bash
adb shell am broadcast -a android.intent.action.BOOT_COMPLETED
```

只向`org.mazhuang.boottimemeasure/.BootCompletedReceiver`广播`BOOT_COMPLETED`：

``` bash
adb shell am broadcast -a android.intent.action.BOOT_COMPLETED -n org.mazhuang.boottimemeasure/.BootCompletedReceiver
```

这类用法在测试的时候很实用，比如某个广播的场景很难制造，可以考虑通过这种方式来发送广播。既能发送系统预定义的广播，也能发送自定义广播。如下是部分系统预定义广播及正常触发时机(以下广播均可使用`adb`触发)：

action                                            | 触发时机
--------------------------------------------------|--------
`android.net.conn.CONNECTIVITY_CHANGE`            | 网络连接发生变化
`android.intent.action.SCREEN_ON`                 | 屏幕点亮
`android.intent.action.SCREEN_OFF`                | 屏幕熄灭
`android.intent.action.BATTERY_LOW`               | 电量低，会弹出电量低提示框
`android.intent.action.BATTERY_OKAY`              | 电量恢复了
`android.intent.action.BOOT_COMPLETED`            | 设备启动完毕
`android.intent.action.DEVICE_STORAGE_LOW`        | 存储空间过低
`android.intent.action.DEVICE_STORAGE_OK`         | 存储空间恢复
`android.intent.action.PACKAGE_ADDED`             | 安装了新的应用
`android.net.wifi.STATE_CHANGE`                   | `WiFi`连接状态发生变化
`android.net.wifi.WIFI_STATE_CHANGED`             | `WiFi`状态变为启用/关闭/正在启动/正在关闭/未知
`android.intent.action.BATTERY_CHANGED`           | 电池电量发生变化
`android.intent.action.INPUT_METHOD_CHANGED`      | 系统输入法发生变化
`android.intent.action.ACTION_POWER_CONNECTED`    | 外部电源连接
`android.intent.action.ACTION_POWER_DISCONNECTED` | 外部电源断开连接
`android.intent.action.DREAMING_STARTED`          | 系统开始休眠
`android.intent.action.DREAMING_STOPPED`          | 系统停止休眠
`android.intent.action.WALLPAPER_CHANGED`         | 壁纸发生变化
`android.intent.action.HEADSET_PLUG`              | 插入耳机
`android.intent.action.MEDIA_UNMOUNTED`           | 卸载外部介质
`android.intent.action.MEDIA_MOUNTED`             | 挂载外部介质
`android.os.action.POWER_SAVE_MODE_CHANGED`       | 省电模式开启

#### 强制停止应用

&emsp;&emsp;命令格式：

``` bash
adb shell am force-stop <packagename>
```

使用示例如下：

``` bash
adb shell am force-stop com.qihoo360.mobilesafe
```

#### 收紧内存

&emsp;&emsp;命令格式：

``` bash
adb shell am send-trim-memory <pid> <level>
```

参数`pid`是进程`ID`；`level`有如下选项：

- `HIDDEN`
- `RUNNING_MODERATE`
- `BACKGROUND`
- `RUNNING_LOW`
- `MODERATE`
- `RUNNING_CRITICAL`
- `COMPLETE`

&emsp;&emsp;如下示例表示向`pid = 12345`的进程发出`level = RUNNING_LOW`的收紧内存命令：

``` bash
adb shell am send-trim-memory 12345 RUNNING_LOW
```

### 文件管理

#### 复制设备里的文件到电脑

&emsp;&emsp;命令格式：

``` bash
adb pull <设备里的文件路径> [电脑上的目录]
```

其中，参数`电脑上的目录`可以省略，默认复制到当前目录：

``` bash
adb pull /sdcard/sr.mp4 ~/tmp/
```

&emsp;&emsp;设备上的文件路径可能需要`root`权限才能访问，如果你的设备已经`root`过，可以先使用`adb shell`和`su`命令在`adb shell`里获取`root`权限后，先`cp /path/on/device /sdcard/filename`将文件复制到`sdcard`，然后`adb pull /sdcard/filename /path/on/pc`。

#### 复制电脑里的文件到设备

&emsp;&emsp;命令格式：

``` bash
adb push <电脑上的文件路径> <设备里的目录>
```

命令示例：

``` bash
adb push ~/sr.mp4 /sdcard/
```

&emsp;&emsp;设备上的文件路径普通权限可能无法直接写入，如果你的设备已经`root`过，可以先`adb push /path/on/pc /sdcard/filename`，然后`adb shell`和`su`在`adb shell`里获取`root`权限后，`cp /sdcard/filename /path/on/device`。

#### 模拟按键/输入

&emsp;&emsp;在`adb shell`里有个很实用的命令叫`input`，通过它可以做一些有趣的事情。`input`命令的完整`help`信息如下：

``` bash
Usage: input [<source>] <command> [<arg>...]
The sources are:
      mouse
      keyboard
      joystick
      touchnavigation
      touchpad
      trackball
      stylus
      dpad
      gesture
      touchscreen
      gamepad

The commands and default sources are:
      text <string> (Default: touchscreen)
      keyevent [--longpress] <key code number or name> ... (Default: keyboard)
      tap <x> <y> (Default: touchscreen)
      swipe <x1> <y1> <x2> <y2> [duration(ms)] (Default: touchscreen)
      press (Default: trackball)
      roll <dx> <dy> (Default: trackball)
```

比如使用`adb shell input keyevent <keycode>`命令，不同的`keycode`能实现不同的功能：

keycode | 含义                          | keycode | 含义
--------|-------------------------------|---------|-----
`3`     | `HOME`键                      | `4`     | 返回键
`5`     | 打开拨号应用                   | `6`     | 挂断电话
`24`    | 增加音量                       | `25`    | 降低音量
`26`    | 电源键                         | `27`    | 拍照(需要在相机应用里)
`64`    | 打开浏览器                     | `82`    | 菜单键
`85`    | 播放/暂停                      | `86`    | 停止播放
`87`    | 播放下一首                     | `88`    | 播放上一首
`122`   | 移动光标到行首或列表顶部        | `123`   | 移动光标到行末或列表底部
`126`   | 恢复播放                       | `127`   | 暂停播放
`164`   | 静音                           | `176`   | 打开系统设置
`187`   | 切换应用                        | `207`   | 打开联系人
`208`   | 打开日历                        | `209`   | 打开音乐
`210`   | 打开计算器                      | `220`   | 降低屏幕亮度
`221`   | 提高屏幕亮度                    | `223`   | 系统休眠
`224`   | 点亮屏幕                        | `231`   | 打开语音助手
`276`   | 如果没有`wakelock`，则让系统休眠

- `电源键`：执行效果相当于按电源键：

``` bash
adb shell input keyevent 26
```

- `菜单键`：命令格式：

``` bash
adb shell input keyevent 82
```

- `HOME键`：命令格式：

``` bash
adb shell input keyevent 3
```

- `返回键`：命令格式：

``` bash
adb shell input keyevent 4
```

- `音量控制`：命令格式：

``` bash
adb shell input keyevent 24  # 增加音量
adb shell input keyevent 25  # 降低音量
adb shell input keyevent 164  # 静音
```

- `媒体控制`：命令格式：

``` bash
adb shell input keyevent 85  # 播放/暂停
adb shell input keyevent 86  # 停止播放
adb shell input keyevent 87  # 播放下一首
adb shell input keyevent 88  # 播放上一首
adb shell input keyevent 126  # 恢复播放
adb shell input keyevent 127  # 暂停播放
```

- `点亮/熄灭屏幕`：命令格式：

``` bash
adb shell input keyevent 224  # 点亮屏幕
adb shell input keyevent 223  # 熄灭屏幕
```

#### 滑动解锁

&emsp;&emsp;如果锁屏没有密码，是通过滑动手势解锁，那么可以通过`input swipe`来解锁，命令如下(参数以机型`Nexus 5`，向上滑动手势解锁举例)：

``` bash
adb shell input swipe 300 1000 300 500
```

参数`300`、`1000`、`300`和`500`分别表示起始点`x`坐标、起始点`y`坐标、结束点`x`坐标和结束点`y`坐标。

#### 输入文本

&emsp;&emsp;在焦点处于某文本框时，可以通过`input`命令来输入文本：

``` bash
adb shell input text hello
```

现在`hello`出现在文本框了。

### 查看日志

&emsp;&emsp;`Android`系统的日志分为两部分，底层的`Linux`内核日志输出到`/proc/kmsg`，`Android`的日志输出到`/dev/log`。

#### Android日志

&emsp;&emsp;命令格式：

``` bash
adb logcat [<option>] ... [<filter-spec>] ...
```

##### 按级别过滤日志

&emsp;&emsp;`Android`的日志分为如下几个优先级：

优先级 | 说明
------|-----
`V`   | `Verbose`(最低，输出的信息最多)
`D`   | `Debug`
`I`   | `Info`
`W`   | `Warning`
`E`   | `Error`
`F`   | `Fatal`
`S`   | `Silent`(最高，什么也不输出)

按某级别过滤日志则会将该级别及以上的日志输出，例如如下命令会将`Warning`、`Error`、`Fatal`和`Silent`日志输出：

``` bash
adb logcat *:W
```

注意，在`macOS`下需要给`*:W`这样以`*`作为`tag`的参数加双引号，例如`adb logcat "*:W"`，不然会报错`no matches found: *:W`。

##### 按tag和级别过滤日志

&emsp;&emsp;`<filter-spec>`可以由多个`<tag>[:priority]`组成。如下命令表示输出`tag ActivityManager`的`Info`以上级别日志，输出`tag MyApp`的`Debug`以上级别日志，及其它`tag`的`Silent`级别日志(即屏蔽其它`tag`日志)：

``` bash
adb logcat ActivityManager:I MyApp:D *:S
```

##### 日志格式

&emsp;&emsp;可以用`adb logcat -v <format>`选项指定日志输出格式。日志支持按以下几种`<format>`：

- `brief`：默认格式：

``` bash
<priority>/<tag>(<pid>): <message>
```

示例如下：

``` bash
D/HeadsetStateMachine(1785): Disconnected process message: 10, size: 0
```

- `process`：格式如下：

``` bash
<priority>(<pid>) <message>
```

示例如下：

``` bash
D(1785) Disconnected process message: 10, size: 0 (HeadsetStateMachine)
```

- `tag`：格式如下：

``` bash
<priority>/<tag>: <message>
```

示例如下：

``` bash
D/HeadsetStateMachine: Disconnected process message: 10, size: 0
```

- `raw`：格式如下：

``` bash
<message>
```

示例如下：

``` bash
Disconnected process message: 10, size: 0
```

- `time`：格式如下：

``` bash
<datetime> <priority>/<tag>(<pid>): <message>
```

示例如下：

``` bash
08-28 22:39:39.974 D/HeadsetStateMachine(1785): Disconnected process message: 10, size: 0
```

- `threadtime`：格式如下：

``` bash
<datetime> <pid> <tid> <priority> <tag>: <message>
```

示例如下：

``` bash
08-28 22:39:39.974 1785 1832 D HeadsetStateMachine: Disconnected process message: 10, size: 0
```

- `long`：格式如下：

``` bash
[ <datetime> <pid>:<tid> <priority>/<tag> ]
<message>
```

示例如下：

``` bash
[ 08-28 22:39:39.974 1785: 1832 D/HeadsetStateMachine ]
Disconnected process message: 10, size: 0
```

&emsp;&emsp;指定格式可与上面的过滤同时使用：

``` bash
adb logcat -v long ActivityManager:I *:S
```

##### 清空日志

&emsp;&emsp;命令格式：

``` bash
adb logcat -c
```

#### 内核日志

&emsp;&emsp;命令格式：

``` bash
adb shell dmesg
```

输出示例：

``` bash
<6>[14201.684016] PM: noirq resume of devices complete after 0.982 msecs
<6>[14201.685525] PM: early resume of devices complete after 0.838 msecs
<6>[14201.753642] PM: resume of devices complete after 68.106 msecs
<4>[14201.755954] Restarting tasks ... done.
<6>[14201.771229] PM: suspend exit 2016-08-28 13:31:32.679217193 UTC
<6>[14201.872373] PM: suspend entry 2016-08-28 13:31:32.780363596 UTC
<6>[14201.872498] PM: Syncing filesystems ... done.
```

`中括号`里的`[14201.684016]`代表内核开始启动后的时间，单位为`秒`。
&emsp;&emsp;通过内核日志，我们可以做一些事情，比如衡量内核启动时间，在系统启动完毕后的内核日志里找到`Freeing init memory`那一行，其前面的时间就是。

### 查看设备信息

#### 型号

&emsp;&emsp;命令格式：

``` bash
adb shell getprop ro.product.model
```

输出示例：

``` bash
Nexus 5
```

#### 电池状况

&emsp;&emsp;命令格式：

``` bash
adb shell dumpsys battery
```

输入示例：

``` bash
Current Battery Service state:
  AC powered: false
  USB powered: true
  Wireless powered: false
  status: 2
  health: 2
  present: true
  level: 44
  scale: 100
  voltage: 3872
  temperature: 280
  technology: Li-poly
```

其中`scale`代表最大电量，`level`代表当前电量。上面的输出表示还剩下`44%`的电量。

#### 屏幕分辨率

&emsp;&emsp;命令格式：

``` bash
adb shell wm size
```

输出示例：

``` bash
Physical size: 1080x1920
```

该设备屏幕分辨率为`1080px * 1920px`。如果使用命令修改过，那么输出可能是：

``` bash
Physical size: 1080x1920
Override size: 480x1024
```

表明设备的屏幕分辨率原本是`1080px * 1920px`，当前被修改为`480px * 1024px`。

#### 屏幕密度

&emsp;&emsp;命令格式：

``` bash
adb shell wm density
```

输出示例：

``` bash
Physical density: 420
```

该设备屏幕密度为`420dpi`。如果使用命令修改过，那输出可能是：

``` bash
Physical density: 480
Override density: 160
```

表明设备的屏幕密度原来是`480dpi`，当前被修改为`160dpi`。

#### 显示屏参数

&emsp;&emsp;命令格式：

``` bash
adb shell dumpsys window displays
```

输出示例：

``` bash
WINDOW MANAGER DISPLAY CONTENTS (dumpsys window displays)
  Display: mDisplayId=0
    init=1080x1920 420dpi cur=1080x1920 app=1080x1794 rng=1080x1017-1810x1731
    deferred=false layoutNeeded=false
```

其中`mDisplayId`为显示屏编号，`init`是初始分辨率和屏幕密度，`app`的高度比`init`里的要小，表示屏幕底部有虚拟按键，高度为`1920 - 1794 = 126px`，即`42dp`。

#### android_id

&emsp;&emsp;命令格式：

``` bash
adb shell settings get secure android_id
```

输出示例：

``` bash
51b6be48bac8c569
```

#### IMEI

&emsp;&emsp;在`Android 4.4`及以下版本，可通过如下命令获取`IMEI`：

``` bash
adb shell dumpsys iphonesubinfo
```

输出示例：

``` bash
Phone Subscriber Info:
  Phone Type = GSM
  Device ID = 860955027785041
```

其中的`Device ID`就是`IMEI`。而在`Android 5.0`及以上版本，这个命令输出为`空`，需要通过其它方式获取(需要`root`权限)：

``` bash
adb shell
su
service call iphonesubinfo 1
```

输出示例：

``` bash
Result: Parcel(
  0x00000000: 00000000 0000000f 00360038 00390030 '........8.6.0.9.'
  0x00000010: 00350035 00320030 00370037 00350038 '5.5.0.2.7.7.8.5.'
  0x00000020: 00340030 00000031                   '0.4.1...        ')
```

把里面的有效内容提取出来就是`IMEI`了，比如这里的是`860955027785041`。

#### Android系统版本

&emsp;&emsp;命令格式：

``` bash
adb shell getprop ro.build.version.release
```

输出示例：

``` bash
5.0.2
```

#### IP地址

&emsp;&emsp;查看设备的`IP`地址可以通过`设置 -> 关于手机 -> 状态信息 -> IP地址`，也可以通过`adb`方便地查看：

``` bash
adb shell ifconfig | grep Mask
```

输出示例：

``` bash
inet addr:10.130.245.230  Mask:255.255.255.252
inet addr:127.0.0.1  Mask:255.0.0.0
```

那么`10.130.245.230`就是设备`IP`地址。在有的设备上，这个命令没有输出，如果设备连着`WiFi`，可以使用如下命令来查看局域网`IP`：

``` bash
adb shell ifconfig wlan0
```

输出示例：

``` bash
wlan0: ip 10.129.160.99 mask 255.255.240.0 flags [up broadcast running multicast]
```

或者：

``` bash
wlan0  Link encap:UNSPEC
       inet addr:10.129.168.57  Bcast:10.129.175.255  Mask:255.255.240.0
       inet6 addr: fe80::66cc:2eff:fe68:b6b6/64 Scope: Link
       UP BROADCAST RUNNING MULTICAST  MTU:1500  Metric:1
       RX packets:496520 errors:0 dropped:0 overruns:0 frame:0
       TX packets:68215 errors:0 dropped:0 overruns:0 carrier:0
       collisions:0 txqueuelen:3000
       RX bytes:116266821 TX bytes:8311736
```

如果以上命令仍然不能得到期望的信息，那么可以试试以下命令(部分系统版本里可用)：

``` bash
adb shell netcfg
```

输出示例：

``` bash
wlan0      UP   10.129.160.99/20 0x00001043 f8:a9:d0:17:42:4d
lo         UP       127.0.0.1/8  0x00000049 00:00:00:00:00:00
p2p0       UP         0.0.0.0/0  0x00001003 fa:a9:d0:17:42:4d
sit0       DOWN       0.0.0.0/0  0x00000080 00:00:00:00:00:00
rmnet0     DOWN       0.0.0.0/0  0x00000000 00:00:00:00:00:00
rmnet1     DOWN       0.0.0.0/0  0x00000000 00:00:00:00:00:00
rmnet3     DOWN       0.0.0.0/0  0x00000000 00:00:00:00:00:00
rmnet2     DOWN       0.0.0.0/0  0x00000000 00:00:00:00:00:00
rmnet4     DOWN       0.0.0.0/0  0x00000000 00:00:00:00:00:00
rmnet6     DOWN       0.0.0.0/0  0x00000000 00:00:00:00:00:00
rmnet5     DOWN       0.0.0.0/0  0x00000000 00:00:00:00:00:00
rmnet7     DOWN       0.0.0.0/0  0x00000000 00:00:00:00:00:00
rev_rmnet3 DOWN       0.0.0.0/0  0x00001002 4e:b7:e4:2e:17:58
rev_rmnet2 DOWN       0.0.0.0/0  0x00001002 4e:f0:c8:bf:7a:cf
rev_rmnet4 DOWN       0.0.0.0/0  0x00001002 a6:c0:3b:6b:c4:1f
rev_rmnet6 DOWN       0.0.0.0/0  0x00001002 66:bb:5d:64:2e:e9
rev_rmnet5 DOWN       0.0.0.0/0  0x00001002 0e:1b:eb:b9:23:a0
rev_rmnet7 DOWN       0.0.0.0/0  0x00001002 7a:d9:f6:81:40:5a
rev_rmnet8 DOWN       0.0.0.0/0  0x00001002 4e:e2:a9:bb:d0:1b
rev_rmnet0 DOWN       0.0.0.0/0  0x00001002 fe:65:d0:ca:82:a9
rev_rmnet1 DOWN       0.0.0.0/0  0x00001002 da:d8:e8:4f:2e:fe
```

可以看到网络连接名称、启用状态、`IP`地址和`MAC`地址等信息。

#### MAC地址

&emsp;&emsp;命令格式：

``` bash
adb shell cat /sys/class/net/wlan0/address
```

输出示例：

``` bash
f8:a9:d0:17:42:4d
```

这查看的是局域网`MAC`地址，移动网络或其它连接的信息可以通过前面的小节`IP地址`里提到的`adb shell netcfg`命令来查看。

#### CPU信息

&emsp;&emsp;命令格式：

``` bash
adb shell cat /proc/cpuinfo
```

输出示例：

``` bash
Processor       : ARMv7 Processor rev 0 (v7l)
processor       : 0
BogoMIPS        : 38.40
processor       : 1
BogoMIPS        : 38.40
processor       : 2
BogoMIPS        : 38.40
processor       : 3
BogoMIPS        : 38.40
Features        : swp half thumb fastmult vfp edsp neon vfpv3 tls vfpv4 idiva idivt
CPU implementer : 0x51
CPU architecture: 7
CPU variant     : 0x2
CPU part        : 0x06f
CPU revision    : 0
Hardware        : Qualcomm MSM 8974 HAMMERHEAD (Flattened Device Tree)
Revision        : 000b
Serial          : 0000000000000000
```

这是`Nexus 5`的`CPU`信息，我们从输出里可以看到使用的硬件是`Qualcomm MSM 8974`，`processor`的编号是`0`到`3`，所以它是`4`核的，采用的架构是`ARMv7 Processor rev 0 (v71)`。

#### 内存信息

&emsp;&emsp;命令格式：

``` bash
adb shell cat /proc/meminfo
```

输出示例：

``` bash
MemTotal:        1027424 kB
MemFree:          486564 kB
Buffers:           15224 kB
Cached:            72464 kB
SwapCached:        24152 kB
Active:           110572 kB
Inactive:         259060 kB
Active(anon):      79176 kB
Inactive(anon):   207736 kB
Active(file):      31396 kB
Inactive(file):    51324 kB
Unevictable:        3948 kB
Mlocked:               0 kB
HighTotal:        409600 kB
HighFree:         132612 kB
LowTotal:         617824 kB
LowFree:          353952 kB
SwapTotal:        262140 kB
SwapFree:         207572 kB
Dirty:                 0 kB
Writeback:             0 kB
AnonPages:        265324 kB
Mapped:            47072 kB
Shmem:              1020 kB
Slab:              57372 kB
SReclaimable:       7692 kB
SUnreclaim:        49680 kB
KernelStack:        4512 kB
PageTables:         5912 kB
NFS_Unstable:          0 kB
Bounce:                0 kB
WritebackTmp:          0 kB
CommitLimit:      775852 kB
Committed_AS:   13520632 kB
VmallocTotal:     385024 kB
VmallocUsed:       61004 kB
VmallocChunk:     209668 kB
```

其中，`MemTotal`就是设备的总内存，`MemFree`是当前空闲内存。

#### 更多硬件与系统属性

&emsp;&emsp;设备的更多硬件与系统属性可以通过如下命令查看：

``` bash
adb shell cat /system/build.prop
```

这会输出很多信息，包括前面提到的`型号`和`Android系统版本`等。
&emsp;&emsp;输出里还包括一些其它有用的信息，它们也可通过`adb shell getprop <属性名>`命令单独查看：

属性名                            | 含义
----------------------------------|---
`ro.build.version.sdk`            | `SDK`版本
`ro.build.version.release`        | `Android`系统版本
`ro.build.version.security_patch` | `Android`安全补丁程序级别
`ro.product.model`                | 型号
`ro.product.brand`                | 品牌
`ro.product.name`                 | 设备名
`ro.product.board`                | 处理器型号
`ro.product.cpu.abilist`          | `CPU`支持的`abi`列表
`persist.sys.isUsbOtgEnabled`     | 是否支持`OTG`
`dalvik.vm.heapsize`              | 每个应用程序的内存上限
`ro.sf.lcd_density`               | 屏幕密度

### 修改设置

&emsp;&emsp;修改设置之后，运行恢复命令有可能显示仍然不太正常，可以运行`adb reboot`重启设备，或手动重启。修改设置的原理主要是修改`/data/data/com.android.providers.settings/databases/settings.db`里存放的设置值。

#### 修改分辨率

&emsp;&emsp;命令格式：

``` bash
adb shell wm size 480x1024
```

表示将分辨率修改为`480px * 1024px`。恢复原分辨率命令：

``` bash
adb shell wm size reset
```

#### 修改屏幕密度

&emsp;&emsp;命令格式：

``` bash
adb shell wm density 160
```

表示将屏幕密度修改为`160dpi`。恢复原屏幕密度命令：

``` bash
adb shell wm density reset
```

#### 修改显示区域

&emsp;&emsp;命令格式：

``` bash
adb shell wm overscan 0,0,0,200
```

四个数字分别表示距离左、上、右、下边缘的留白像素，以上命令表示将屏幕底部`200px`留白。恢复原显示区域命令：

``` bash
adb shell wm overscan reset
```

#### 关闭USB调试模式

&emsp;&emsp;命令格式：

``` bash
adb shell settings put global adb_enabled 0
```

如果想要恢复调试模式，用命令无法恢复，毕竟关闭了`USB`调试，`adb`就连接不上`Android`设备了，可以在设备上手动恢复。

#### 允许/禁止访问非SDK API

&emsp;&emsp;允许访问非`SDK API`：

``` bash
adb shell settings put global hidden_api_policy_pre_p_apps 1
adb shell settings put global hidden_api_policy_p_apps 1
```

禁止访问非`SDK API`：

``` bash
adb shell settings delete global hidden_api_policy_pre_p_apps
adb shell settings delete global hidden_api_policy_p_apps
```

不需要设备获得`Root`权限。命令最后的数字的含义：

值  | 含义
----|----
`0` | 禁止检测非`SDK`接口的调用。该情况下，日志记录功能被禁用，并且令`strict mode API`，即`detectNonSdkApiUsage`无效，不推荐
`1` | 仅警告，即允许访问所有非`SDK`接口，但保留日志中的警告信息，可继续使用`strick mode API`
`2` | 禁止调用深灰名单和黑名单中的接口
`3` | 禁止调用黑名单中的接口，但允许调用深灰名单中的接口

#### 状态栏和导航栏的显示隐藏

&emsp;&emsp;本节所说的相关设置对应`Cyanogenmod`里的`扩展桌面`，命令格式如下：

``` bash
adb shell settings put global policy_control <key-values>
```

`<key-values>`可由如下几种键及其对应的值组成，格式为`<key1>=<value1>:<key2>=<value2>`：

key                    | 含义
-----------------------|----
`immersive.full`       | 同时隐藏
`immersive.status`     | 隐藏状态栏
`immersive.navigation` | 隐藏导航栏

这些键对应的值可则如下值用逗号组合：

value          | 含义
---------------|----
`apps`         | 所有应用
`*`            | 所有界面
`packagename`  | 指定应用
`-packagename` | 排除指定应用

如下命令表示设置在所有界面下都同时隐藏状态栏和导航栏：

``` bash
adb shell settings put global policy_control immersive.full=*
```

如下命令表示设置在包名为`com.package1`和`com.package2`的应用里隐藏状态栏，在除了包名为`com.package3`的所有应用里隐藏导航栏：

``` bash
adb shell settings put global policy_control immersive.status=com.package1,com.package2:immersive.navigation=apps,-com.package3
```

### 实用功能

#### 屏幕截图

&emsp;&emsp;截图保存到电脑：

``` bash
adb exec-out screencap -p > sc.png
```

如果`adb`版本较老，无法使用`exec-out`命令，这时候建议更新`adb`版本。如果无法更新的话，可以使用以下麻烦一点的办法：

``` bash
adb shell screencap -p /sdcard/sc.png  # 先截图保存到设备里
adb pull /sdcard/sc.png  # 然后将png文件导出到电脑
```

可以使用`adb shell screencap -h`查看`screencap`命令的帮助信息，下面是两个有意义的参数及含义：

参数            | 含义
----------------|---
`-p`            | 指定保存文件为`png`格式
`-d display-id` | 指定截图的显示屏编号(有多显示屏的情况下)

如果指定文件名以`.png`结尾时，可以省略`-p`参数，否则需要使用`-p`参数。如果不指定文件名，截图文件的内容将直接输出到`stdout`。

#### 录制屏幕

&emsp;&emsp;录制屏幕以`mp4`格式保存到`/sdcard`：

``` bash
adb shell screenrecord /sdcard/filename.mp4
```

需要停止时，按下`Ctrl-C`，默认录制时间和最长录制时间都是`180`秒。如果需要导出到电脑，则使用如下命令：

``` bash
adb pull /sdcard/filename.mp4
```

可以使用`adb shell screenrecord --help`查看`screenrecord`命令的帮助信息，下面是常见参数及含义：

参数                  | 含义
----------------------|----
`--size WIDTHxHEIGHT` | 视频的尺寸，比如`1280 x 720`，默认是屏幕分辨率
`--bit-rate RATE`     | 视频的比特率，默认是`4Mbps`
`--time-limit TIME`   | 录制时长，单位是秒
`--verbose`           | 输出更多信息

#### 重新挂载system分区为可写

&emsp;&emsp;该步骤需要`root`权限。`/system`分区默认挂载为只读，但有些操作比如给`Android`系统添加命令、删除自带应用等需要对`/system`进行写操作，所以需要重新挂载它为可读写。
&emsp;&emsp;进入`shell`，并切换到`root`用户权限：

``` bash
adb shell
su
```

查看当前分区挂载情况，使用如下命令：

``` bash
mount
```

输出示例：

``` bash
rootfs / rootfs ro,relatime 0 0
tmpfs /dev tmpfs rw,seclabel,nosuid,relatime,mode=755 0 0
devpts /dev/pts devpts rw,seclabel,relatime,mode=600 0 0
proc /proc proc rw,relatime 0 0
sysfs /sys sysfs rw,seclabel,relatime 0 0
selinuxfs /sys/fs/selinux selinuxfs rw,relatime 0 0
debugfs /sys/kernel/debug debugfs rw,relatime 0 0
none /var tmpfs rw,seclabel,relatime,mode=770,gid=1000 0 0
none /acct cgroup rw,relatime,cpuacct 0 0
none /sys/fs/cgroup tmpfs rw,seclabel,relatime,mode=750,gid=1000 0 0
none /sys/fs/cgroup/memory cgroup rw,relatime,memory 0 0
tmpfs /mnt/asec tmpfs rw,seclabel,relatime,mode=755,gid=1000 0 0
tmpfs /mnt/obb tmpfs rw,seclabel,relatime,mode=755,gid=1000 0 0
none /dev/memcg cgroup rw,relatime,memory 0 0
none /dev/cpuctl cgroup rw,relatime,cpu 0 0
none /sys/fs/cgroup tmpfs rw,seclabel,relatime,mode=750,gid=1000 0 0
none /sys/fs/cgroup/memory cgroup rw,relatime,memory 0 0
none /sys/fs/cgroup/freezer cgroup rw,relatime,freezer 0 0
/dev/block/platform/msm_sdcc.1/by-name/system /system ext4 ro,seclabel,relatime,data=ordered 0 0
...
```

找到其中带有`/system`的那一行：

``` bash
/dev/block/platform/msm_sdcc.1/by-name/system /system ext4 ro,seclabel,relatime,data=ordered 0 0
```

重新挂载，使用如下命令：

``` bash
mount -o remount,rw -t yaffs2 /dev/block/platform/msm_sdcc.1/by-name/system /system
```

这里的`/dev/block/platform/msm_sdcc.1/by-name/system`就是我们从上一步的输出里得到的文件路径。如果输出没有提示错误的话，操作就成功了，可以对`/system`下的文件为所欲为了。

#### 查看连接过的WiFi密码

&emsp;&emsp;该步骤需要`root`权限，使用如下命令：

``` bash
adb shell
su
cat /data/misc/wifi/*.conf
```

输出示例：

``` bash
network={
    ssid="TP-LINK_9DFC"
    scan_ssid=1
    psk="123456789"
    key_mgmt=WPA-PSK
    group=CCMP TKIP
    auth_alg=OPEN
    sim_num=1
    priority=13893
}

network={
    ssid="TP-LINK_F11E"
    psk="987654321"
    key_mgmt=WPA-PSK
    sim_num=1
    priority=17293
}
```

`ssid`即为我们在`WLAN`设置里看到的名称，`psk`为密码，`key_mgmt`为安全加密方式。

#### 设置系统日期和时间

&emsp;&emsp;该步骤需要`root`权限，使用如下命令：

``` bash
adb shell
su
date -s 20160823.131500
```

表示将系统日期和时间更改为`2016`年`08`月`23`日`13`点`15`分`00`秒。

#### 重启手机

&emsp;&emsp;命令格式：

``` bash
adb reboot
```

#### 检测设备是否已root

&emsp;&emsp;命令格式：

``` bash
adb shell
su
```

此时命令行提示符是`$`，则表示没有`root`权限，是`#`则表示已`root`。

#### 使用Monkey进行压力测试

&emsp;&emsp;`Monkey`可以生成伪随机用户事件来模拟单击、触摸、手势等操作，可以对正在开发中的程序进行随机压力测试。简单用法如下：

``` bash
adb shell monkey -p <packagename> -v 500
```

表示向`<packagename>`指定的应用程序发送`500`个伪随机事件。

#### 开启/关闭WiFi

&emsp;&emsp;该步骤需要`root`权限，有时需要控制设备的`WiFi`状态，可以用以下指令完成：

``` bash
# 开启WiFi
adb root
adb shell svc wifi enable
# 关闭WiFi
adb root
adb shell svc wifi disable
```

若执行成功，输出为空；若未取得`root`权限执行此命令，将执行失败，输出`Killed`。

#### 刷机相关命令

&emsp;&emsp;命令如下：

``` bash
adb reboot recovery  # 重启到Recovery模式
adb reboot  # 从Recovery重启到Android
adb reboot bootloader  # 重启到Fastboot模式
```

### 更多adb shell命令

&emsp;&emsp;`Android`系统是基于`Linux`内核的，所以`Linux`里的很多命令在`Android`里也有相同或类似的实现，在`adb shell`里可以调用。本文档前面的部分内容已经用到了`adb shell`命令。

#### 查看进程

&emsp;&emsp;命令格式：

``` bash
adb shell ps
```

输出示例：

``` bash
USER   PID  PPID VSIZE   RSS   WCHAN    PC         NAME
root   1    0    8904    788   ffffffff 00000000 S /init
root   2    0    0       0     ffffffff 00000000 S kthreadd
...
u0_a71 7779 5926 1538748 48896 ffffffff 00000000 S com.sohu.inputmethod.sogou:classic
u0_a58 7963 5926 1561916 59568 ffffffff 00000000 S org.mazhuang.boottimemeasure
...
shell  8750 217  10640   740   00000000 b6f28340 R ps
```

各列含义：

列名   | 含义
-------|----
`USER` | 所属用户
`PID`  | 进程`ID`
`PPID` | 父进程`ID`
`NAME` | 进程名

#### 查看实时资源占用情况

&emsp;&emsp;命令格式：

``` bash
adb shell top
```

输出示例：

``` bash
User 0%, System 6%, IOW 0%, IRQ 0%
User 3 + Nice 0 + Sys 21 + Idle 280 + IOW 0 + IRQ 0 + SIRQ 3 = 307

  PID PR CPU% S  #THR     VSS     RSS PCY UID      Name
 8763  0   3% R     1  10640K   1064K  fg shell    top
  131  0   3% S     1      0K      0K  fg root     dhd_dpc
 6144  0   0% S   115 1682004K 115916K  fg system   system_server
  132  0   0% S     1      0K      0K  fg root     dhd_rxf
 1731  0   0% S     6  20288K    788K  fg root     /system/bin/mpdecision
  217  0   0% S     6  18008K    356K  fg shell    /sbin/adbd
...
 7779  2   0% S    19 1538748K  48896K  bg u0_a71   com.sohu.inputmethod.sogou:classic
 7963  0   0% S    18 1561916K  59568K  fg u0_a58   org.mazhuang.boottimemeasure
...
```

各列含义：

列名   | 含义
-------|----
`PID`  | 进程`ID`
`PR`   | 优先级
`CPU%` | 当前瞬间占用`CPU`百分比
`S`    | 进程状态(`R`是运行，`S`是睡眠，`T`是跟踪或停止，`Z`是僵尸进程)
`#THR` | 线程数
`VSS`  | `Virtual Set Size`虚拟耗用内存(包含共享库占用的内存)
`RSS`  | `Resident Set Size`实际使用物理内存(包含共享库占用的内存)
`PCY`  | 调度策略优先级，例如`SP_BACKGROUND`、`SPFOREGROUND`
`UID`  | 进程所有者的用户`ID`
`NAME` | 进程名

`top`命令还支持一些命令行参数，详细用法如下：

``` bash
Usage: top [ -m max_procs ] [ -n iterations ] [ -d delay ] [ -s sort_column ] [ -t ] [ -h ]
    -m num  最多显示多少个进程
    -n num  刷新多少次后退出
    -d num  刷新时间间隔(单位秒，默认值5)
    -s col  按某列排序(可用col值：cpu、vss、rss和thr)
    -t      显示线程信息
    -h      显示帮助文档
```

#### 其它

&emsp;&emsp;如下是其它常用命令的简单描述：

命令    | 功能
--------|----
`cat`   | 显示文件内容
`cd`    | 切换目录
`chmod` | 改变文件的存取模式或访问权限
`df`    | 查看磁盘空间使用情况
`grep`  | 过滤输出
`kill`  | 杀死指定`PID`的进程
`ls`    | 列举目录内容
`mount` | 挂载目录的查看和管理
`mv`    | 移动或重命名文件
`ps`    | 查看正在运行的进程
`rm`    | 删除文件
`top`   | 查看进程的资源占用情况

### 常见问题

#### 启动“adb server”失败

&emsp;&emsp;出错提示：

``` bash
error: protocol fault (couldn't read status): No error
```

可能原因是`adb server`进程想使用的`5037`端口被占用。解决方案是找到占用`5037`端口的进程，然后终止它。以`Windows`系统为例：

``` bash
netstat -ano | findstr LISTENING
...
TCP  0.0.0.0:5037  0.0.0.0:0  LISTENING  1548
...
```

这里的`1548`即为进程`ID`，用命令结束该进程：

``` bash
taskkill /PID 1548
```

然后再启动`adb`就没问题了。