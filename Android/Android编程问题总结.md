---
title: Android编程问题总结
categories: Android
abbrlink: '46894180'
date: 2018-12-28 13:43:40
---
### failed to load AppCompat ActionBar with unknown error

&emsp;&emsp;这种错误导致`Android Studio`无法预览`xml`布局视图。解决方法是找到`res -> values -> styles.xml`文件，将`parent="Theme.AppCompat.Light.DarkActionBar"`改为`parent="Base.Theme.AppCompat.Light.DarkActionBar"`。

### 使用设备文件浏览器查看设备上的文件

&emsp;&emsp;利用设备文件浏览器，您可以在`Android`设备上查看、复制和删除文件。如果您要检查您的应用创建的文件，或者想要在设备上双向传输文件，这样将非常有用。
&emsp;&emsp;要处理设备的文件系统，首先点击`View -> Tool Windows -> Device File Explorer`或工具窗口栏中的`Device File Explorer`按钮以打开设备文件浏览器，然后从下拉列表中选择设备，最后在文件浏览器中对文件或目录进行操作。
&emsp;&emsp;浏览设备的文件时，以下目录特别有用：

- `data/data/app_name/`：包含您的应用存储在内部存储空间中的数据文件。
- `sdcard/`：包含存储在外部用户存储空间中的用户文件(例如图片)。

注意，并不是硬件设备上的所有文件在设备文件浏览器中都是可见的。例如在`data/data/`目录中，与设备上的不可调试应用对应的条目就无法在设备文件浏览器中展开。

### Toast在线程中无法显示

&emsp;&emsp;这是因为`Android`不允许在子线程中更改UI，因此需要子线程通过`handler`传递接收数据来显示`Toast`。

### compressToJpeg导致的内存泄漏

&emsp;&emsp;如果将`compressToJpeg`放在线程中运行时，可能会导致内存泄漏，因此最好将其放在进程中。