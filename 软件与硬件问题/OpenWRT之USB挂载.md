---
title: OpenWRT之USB挂载
categories: 软件与硬件问题
abbrlink: 68e805db
date: 2019-02-06 11:40:02
---
&emsp;&emsp;1. 更新路由器软件源(路由器需要联网)：

``` bash
opkg update
```

&emsp;&emsp;2. 添加`USB`支持：

``` bash
opkg install kmod-usb-core
opkg install kmod-usb-uhci
opkg install kmod-usb-storage
opkg install kmod-usb2
opkg install kmod-usb-ohci
```

&emsp;&emsp;3. 添加`usb`挂载、热插拔以及`boot`支持：

``` bash
opkg install block-mount    # 挂载
opkg install block-hotplug  # 热插拔
opkg install block-extroot  # boot支持
```

此时可以插上`U`盘测试一下：

``` cpp
ls -l /dev | grep sda
```

显示结果如下：

``` cpp
root@Openwrt:/#ls -l /dev | grep sda
brw-r--r--    1    root    root    8,    0    Jan    1    1970    sda
```

观察里面是否出现`sda`、`sda1`、`sda2`、`sda3`等字样。`sda`说明是名为`sda`的设备，而`sda1`是它的第一个分区，后面依次类推。