---
title: 添加驱动程序到Linux内核
date: 2019-02-04 10:04:14
categories: Linux驱动程序
---
&emsp;&emsp;添加驱动程序到内核步骤为：添加驱动程序到内核源代码位置；配置内核；编译内核；下载运行测试。

### 添加驱动程序到内核

&emsp;&emsp;`Linux`内核的配置系统由以下`3`个部分组成：

- `Makefile`：分布在`Linux`内核源代码中的`Makefile`定义内核的编译规则。
- 配置文件`Kconfig`：给用户提供配置选择的功能。
- 配置工具：包括配置命令解释器(对配置脚本中使用的配置命令进行解释)、配置用户界面(提供字符界面和图形界面)，这些配置工具都是使用脚本语言(`Perl`等)编写的。

&emsp;&emsp;在`Linux`内核中增加程序需要完成以下`3`项工作：

- 将编写的源代码复制到`linux`内核源代码的相应目录。
- 在目录的`Kconfig`文件中增加新源代码对应项目的编译配置选项。
- 在目录的`Makefile`文件中增加对新源代码的编译条目。

&emsp;&emsp;添加`BEEP`驱动程序到内核：

- 方法一：在`linux-2.6.32.2/drivers/char/`目录下直接添加`beep_drv.c`源文件。
- 方法二：在`linux-2.6.32.2/drivers/char/`目录下添加`beep`驱动目录。

### 添加BEEP驱动程序到内核(方法一)

&emsp;&emsp;添加`Beep`设备的内核配置选项，打开`drivers/char/Kconfig`文件，添加如下内容：

``` makefile
config BEEP_MINI2440
    tristate "BEEP Driver Support for Mini2440 BEEP Test"
    depends on MACH_MINI2440
    default y if MACH_MINI2440
    help
        This option enables support for BEEP connectded to GPIO lines on Mini2440 boards.
```

### 配置编译新内核

&emsp;&emsp;在内核源代码目录下执行命令`make menuconfig`，重新配置内核，依次选择进入如下子菜单：

``` bash
Device Drivers --->
    Character devices --->
```

根据该驱动的配置定义，把对应的驱动目标文件加入内核中。打开`linux-2.6.32.2/drivers/char/Makefile`文件，添加如下内容：

``` makefile
obj-$(CONFIG_BEEP_MINI2440) += beep_drv.o
```

在内核`linux-2.6.32.2/drivers/char/`目录下，新建`BEEP`驱动文件`beep_drv.c`。

### 添加BEEP驱动程序到内核(方法二)

&emsp;&emsp;在`linux-2.6.32.2/drivers/char/`目录下新建`BEEP`驱动目录，其树形目录如下：

``` cpp
|-- beep
    |-- beep_drv.c
    |-- Konfig
    |-- Makefile
```

步骤如下：

- 在`drivers/char/`路径下新建`beep`目录。
- 在`beep`目录下添加`beep_drv.c`文件。
- 在`beep`目录下创建`Kconfig`和`Makefile`。
- 修改新增目录的父目录的`Kconfig`和`Makefile`，以便新增的`Kconfig`和`Makefile`能够被引用。

&emsp;&emsp;在`drivers/char/beep/Kconfig`文件中添加如下代码：

``` cpp
config BEEP_MINI2440
    tristate "BEEP Driver Support for Mini2440 BEEP Test"
    depends on MACH_MINI2440
    default y if MACH_MINI2440
    help
        This option enables support for BEEP connectded to GPIO lines on Mini2440 boards.
```

&emsp;&emsp;在`drivers/char/beep/Makefile`文件中添加如下代码：

``` makefile
obj-$(CONFIG_BEEP_MINI2440) += beep_drv.o
```

修改新增目录的父目录的`Kconfig`和`Makefile`，在`drivers/char/Kconfig`中加入：

``` bash
source "driver/char/beep/Kconfig"
```

在`drivers/char/Makefile`中加入：

``` makefile
obj-$(CONFIG_BEEP_MINI2440) += beep/
```

&emsp;&emsp;在内核源代码目录下执行命令`make menuconfig`，重新配置内核，依次选择进入如下子菜单：

``` bash
Device Drivers --->
    Character devices --->
```

进入`Linux`内核根目录重新编译内核：

``` bash
cd /opt/linux-2.6.32.2
make zImage
```

进入目录`/arch/arm/boot`，将新生成的`zImage`转换成`uImage`，通过`uboot`下载`uImage`到开发板。

### 编写BEEP测试程序

&emsp;&emsp;1. 编写`BEEP`测试程序`beep_test.c`，完成对`BEEP`的调试。
&emsp;&emsp;2. 上电开发板，运行新的`uImage`内核。
&emsp;&emsp;3. 使用命令`cat /proc/devices | grep beep`查看`BEEP`驱动系统自动分配的主设备号。
&emsp;&emsp;4. 使用命令`mknod /dev/beep c major 0`制作`BEEP`对应的设备文件节点。
&emsp;&emsp;5. 将编译生产的`beep_test`，下载到开发板并测试`BEEP`驱动(使用命令`./beep_test`)。