---
title: openWRT模块开发
categories: 嵌入式笔记
abbrlink: 112f755a
date: 2019-01-18 09:09:08
---
&emsp;&emsp;在`openwrt`源码目录下`make menuconfig`时，选择`[*] Build the OpenWrt SDK`，之后就会在其`bin/ar71xx/`下产生`SDK`文件`OpenWrt-SDK-ar71xx-for-linux-i686-gcc-4.6-linaro_uClibc-0.9.33.2.tar.bz2`”。解压之后就是`OpenWrt-SDK-ar71xx-for-linux-i686-gcc-4.6-linaro_uClibc-0.9.33.2`，此目录结构跟`openwrt`的目录结构基本一致。
&emsp;&emsp;注意，模块的源码在`SDK/pakage`目录，生成的`ipk`文件在`SDK/bin/ar71xx/packages`目录。如果没有`SDK/bin/ar71xx/packages`目录，也可手动创建。主要步骤如下：
&emsp;&emsp;1. 在`OpenWrt-SDK-ar71xx-for-linux-i686-gcc-4.6-linaro_uClibc-0.9.33.2/package`目录下新建`helloworld`目录。
&emsp;&emsp;2. 在`helloworld`目录下新建`Makefile`文件和`src`目录。此`Makefile`内容如下：

``` makefile
##############################################
# OpenWrt Makefile for helloworld program
#
# Most of the variables used here are defined in
# the include directives below. We just need to
# specify a basic description of the package,
# where to build our program, where to find
# the source files, and where to install the
# compiled program on the router.
#  
# Be very careful of spacing in this file.
# Indents should be tabs, not spaces, and
# there should be no trailing whitespace in
# lines that are not commented.
#  
##############################################
​
include $(TOPDIR)/rules.mk
​
# Name and release number of this package
PKG_NAME:=helloworld
PKG_RELEASE:=1
​
# This specifies the directory where we're going to build the program.
# The root build directory, $(BUILD_DIR), is by default the build_mipsel
# directory in your OpenWrt SDK directory
PKG_BUILD_DIR := $(BUILD_DIR)/$(PKG_NAME)
​
include $(INCLUDE_DIR)/package.mk
​
# Specify package information for this program.
# The variables defined here should be self explanatory.
# If you are running Kamikaze, delete the DESCRIPTION
# variable below and uncomment the Kamikaze define
# directive for the description below
define Package/helloworld
    SECTION:=utils
    CATEGORY:=Utilities
    TITLE:=Helloworld -- prints a snarky message
endef
​
# Uncomment portion below for Kamikaze and delete DESCRIPTION variable above
define Package/helloworld/description
    If you can't figure out what this program does, you're probably
    brain-dead and need immediate medical attention.
endef
​
# Specify what needs to be done to prepare for building the package.
# In our case, we need to copy the source files to the build directory.
# This is NOT the default.  The default uses the PKG_SOURCE_URL and the
# PKG_SOURCE which is not defined here to download the source from the web.
# In order to just build a simple program that we have just written, it is
# much easier to do it this way.
define Build/Prepare
    mkdir -p $(PKG_BUILD_DIR)
    $(CP) ./src/* $(PKG_BUILD_DIR)/
endef
​
# We do not need to define Build/Configure or Build/Compile directives
# The defaults are appropriate for compiling a simple program such as this one
​
# Specify where and how to install the program. Since we only have one file,
# the helloworld executable, install it by copying it to the /bin directory on
# the router. The $(1) variable represents the root directory on the router running
# OpenWrt. The $(INSTALL_DIR) variable contains a command to prepare the install
# directory if it does not already exist.  Likewise $(INSTALL_BIN) contains the
# command to copy the binary file from its current location (in our case the build
# directory) to the install directory.
define Package/helloworld/install
    $(INSTALL_DIR) $(1)/bin
    $(INSTALL_BIN) $(PKG_BUILD_DIR)/helloworld $(1)/bin/
endef

# This line executes the necessary commands to compile our program.
# The above define directives specify all the information needed, but this
# line calls BuildPackage which in turn actually uses this information to
# build a package.
$(eval $(call BuildPackage,helloworld))
```

&emsp;&emsp;3. 在`src`目录下新建`helloworld.c`文件和`Makefile`文件。`helloworld.c`如下：

``` c
#include <stdio.h>
​
int main ( void ) {
    printf ( "hello_world\n" );
    return 0;
}
```

`Makefile`文件如下：

``` makefile
# build helloworld executable when user executes "make"
helloworld: helloworld.o
    $(CC) $(LDFLAGS) helloworld.o -o helloworld
​
helloworld.o: helloworld.c
    $(CC) $(CFLAGS) -c helloworld.c
​
# remove object files and executable when user executes "make clean"
clean:
    rm *.o helloworld
```

&emsp;&emsp;4. 在`OpenWrt-SDK-ar71xx-for-linux-i686-gcc-4.6-linaro_uClibc-0.9.33.2`目录下执行编译：

``` bash
make  # 或者“make V=s”
```

&emsp;&emsp;5. 成功之后，会在`OpenWrt-SDK-ar71xx-for-linux-i686-gcc-4.6-linaro_uClibc-0.9.33.2/bin/ar71xx/packages/`下生产`helloworld_1_ar71xx.ipk`，将它拷贝到`openWRT`系统中，执行安装命令：

``` bash
opkg install helloworld_1_ar71xx.ipk
```

然后在`openWRT`系统中执行`helloworld`命令，观察程序运行效果。

&emsp;&emsp;**补充说明**：
&emsp;&emsp;1. 在编译前需要安装`ccache`，执行命令`sudo apt-get install ccache`。
&emsp;&emsp;2. `SDK`开发环境也可以在`openwrt`官网中下载。
&emsp;&emsp;3. 不要轻易地在`SDK`的根目录下执行`make distclean`，否则在编译模块代码时会出现错误。解决方法：删除原来的`SDK`开发环境，再重新安装一个新的开发环境。