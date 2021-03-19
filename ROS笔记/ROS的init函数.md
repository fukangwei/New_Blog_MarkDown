---
title: ROS的init函数
date: 2021-03-20 18:50:52
categories: ROS笔记
---
&emsp;&emsp;使用任何`roscpp`函数前，必须调用`ros::init`函数。一般有如下形式：<!--more-->

``` cpp
ros::init ( argc, argv, "my_node_name" );
ros::init ( argc, argv, "my_node_name", ros::init_options::AnonymousName );
ros::init ( argc, argv, "my_node_name", ros::init_options::NoSigintHandler );
```

&emsp;&emsp;`ROS`系统不允许节点的名字出现重复，如果再运行一个，前一个节点会自动关闭。使用`ros::init_options::AnonymousName`就可以同时运行多个同名节点，`ROS`会在节点名后面加上`UTC`时间以示区别。
&emsp;&emsp;运行同一个节点还有更好的方法：比如已经使用命令`rosrun pub Pub`运行了一个名为`Pub`的节点，我们可以指定参数`__name`运行同一个可执行文件，但是节点名不同：

``` bash
rosrun pub Pub __name:=newPub
```