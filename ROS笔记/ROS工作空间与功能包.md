---
title: ROS工作空间与功能包
date: 2021-03-12 22:38:50
categories: ROS笔记
---
### 工作空间

&emsp;&emsp;工作空间(`workspace`)是一个存放工程开发相关文件的文件夹。<!--more-->

- `src`：代码空间(`Source Space`)
- `build`：编译空间(`Build Space`)
- `devel`：开发空间(`Development Space`)
- `install`：安装空间(`Install Space`)

&emsp;&emsp;标准的`Workspace`目录结构如下：

<img src="./ROS工作空间与功能包/Workspace工作目录结构.png" width="20%">

&emsp;&emsp;通过无人驾驶项目来理解`Workspace`：

<img src="./ROS工作空间与功能包/无人驾驶项目.png" width="50%">

&emsp;&emsp;基于`ros`开发一个无人驾驶的小车，代码结构按照`package`划分，可以划分如下：

- `Camera`视觉包。
- `Hardware`硬件包。
- `Motion`控制包。

其中的视觉功能模块就是一个`package`，而该模块下的视频采集功能就是一个`Node`。

### 创建工作空间

&emsp;&emsp;创建一个完整的工作空间需要进行如下的步骤：
&emsp;&emsp;1. 创建工作空间。创建一个名为`catkin_ws`的工作空间，并且在其中创建`src`目录：

``` bash
$ mkdir -p ~/catkin_ws/src
$ cd ~/catkin_ws/src
$ catkin_init_workspace
```

&emsp;&emsp;2. 编译工作空间。

``` bash
$ cd ~/catkin_ws/src
$ catkin_make
```

&emsp;&emsp;3. 设置环境变量。

``` bash
$ source devel/setup.bash
```

&emsp;&emsp;4. 检查环境变量。

``` bash
$ echo $ROS_PACKAGE_PATH
```

&emsp;&emsp;5. 建立`install`空间。

``` bash
$ catkin_make install
```

### 创建功能包

&emsp;&emsp;创建一个完整的功能包需要使用`catkin_create_pkg`，其命令格式如下：

``` bash
catkin_create_pkg <package_name> [depend_1] [depend_2] ... [depend_N]
```

- `<package_name>`：功能包的名字。
- `[depend_N]`：`package`需要的环境依赖。

&emsp;&emsp;创建一个功能包的实例如下：

``` bash
$ cd ~/catkin_ws/src
$ catkin_create_pkg test_pkg roscpp rospy std_msgs
```

- `roscpp`是对`c++`的一种依赖，有了它就可以用`c++`开发`ros`程序。
- `rospy`是对`python`的一种依赖，有了它就可以用`python`开发`ros`程序。
- `std_msgs`是消息传输时常用的依赖。

这样就会在当前路径下新建`test_pkg`软件包，包括如下内容：

``` bash
├── CMakeLists.txt
├── include
│   └── test_pkg
├── package.xml
├── src
└── scripts # 该目录是手动创建，用于放置python脚本
```

&emsp;&emsp;编译功能包：

``` bash
$ cd ~/catkin_ws
$ catkin_make
$ source ~/catkin_ws/devel/setup.bash
```