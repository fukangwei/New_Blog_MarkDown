---
title: ROS命令行工具
date: 2021-03-11 20:07:59
categories: ROS笔记
---
### rqt_graph

&emsp;&emsp;`rqt_graph`创建一个显示当前系统运行情况的动态图形：<!--more-->

``` bash
$ rqt_graph
```

<img src="./ROS命令行工具/rqt_graph.png" width="50%">

### rosnode

&emsp;&emsp;`rosnode`命令可以查看节点相关的信息。
&emsp;&emsp;`rosnode list`用于列出系统所有节点：

``` bash
$ rosnode list
/rosout
/teleop_turtle
/turtlesim
```

&emsp;&emsp;`rosnode info`用于查看某一节点的具体信息：

``` bash
$ rosnode info /turtlesim
------------------------------------------
Node [/turtlesim]
Publications:
 * /rosout [rosgraph_msgs/Log]
 * /turtle1/color_sensor [turtlesim/Color]
 * /turtle1/pose [turtlesim/Pose]

Subscriptions:
 * /turtle1/cmd_vel [geometry_msgs/Twist]

Services:
 * /clear
 * /kill
 * /reset
 * /spawn
 * /turtle1/set_pen
 * /turtle1/teleport_absolute
 * /turtle1/teleport_relative
 * /turtlesim/get_loggers
 * /turtlesim/set_logger_level
```

### rostopic

&emsp;&emsp;`rostopic`工具能让你获取有关`ROS`话题的信息。
&emsp;&emsp;`rostopic list`用于打印系统当前的话题列表：

``` bash
$ rostopic list
/rosout
/rosout_agg
/turtle1/cmd_vel
/turtle1/color_sensor
/turtle1/pose
```

&emsp;&emsp;`rostopic type`命令用来查看所发布话题的消息类型：

``` bash
$ rostopic type /turtle1/cmd_vel
geometry_msgs/Twist
```

&emsp;&emsp;`rostopic pub`用于向话题发布消息：

``` bash
$ rostopic pub /turtle1/cmd_vel geometry_msgs/Twist "linear:
  x: 0.0
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 0.0"
```

- `/turtle1/cmd_vel`：话题名。
- `geometry_msgs/Twist`：话题消息类别。
- `linear:...`：消息内容，其中`linear`是线速度，`angular`是角速度。

&emsp;&emsp;以上命令只能让消息发布一次，如果需要不断地发布消息，则需要使用`-r`选项：

``` bash
# 10表示1秒发布10次
$ rostopic pub -r 10 /turtle1/cmd_vel geometry_msgs/Twist "linear:
  x: 1.0
  y: 0.0
  z: 0.0
angular:
  x: 0.0
  y: 0.0
  z: 1.0"
```

### rosmsg

&emsp;&emsp;我们可以使用`rosmsg`命令来查看消息的详细情况：

``` bash
$ rosmsg show geometry_msgs/Twist
geometry_msgs/Vector3 linear
  float64 x
  float64 y
  float64 z
geometry_msgs/Vector3 angular
  float64 x
  float64 y
  float64 z
```

### rosservice

&emsp;&emsp;和服务有关的功能可以使用`rosservice`命令来进行。
&emsp;&emsp;`rosservice list`用于列出活动的服务信息：

``` bash
$ rosservice list
/clear
/kill
/reset
/rosout/get_loggers
/rosout/set_logger_level
/spawn
/teleop_turtle/get_loggers
/teleop_turtle/set_logger_level
/turtle1/set_pen
/turtle1/teleport_absolute
/turtle1/teleport_relative
/turtlesim/get_loggers
/turtlesim/set_logger_level
```

&emsp;&emsp;`rosservice call`使用输入的参数来请求服务，命令格式为`rosservice call [服务名称] [参数]`：

``` bash
$ rosservice call /clear # “/clear”用于清除小乌龟的轨迹
# 以下命令用于生成新的乌龟
$ rosservice call /spawn "x: 0.0
y: 0.0
theta: 0.0
name: 'turtle2'"
```

&emsp;&emsp;`rosservice args [服务名称]`用于输出服务所需的参数。我们来看看`/turtle1/set_pen`服务的每个参数：

``` bash
$ rosservice args /turtle1/set_pen
r g b width off
```

该命令显示在`/turtle1/set_pen`服务中使用的参数为`r`、`g`、`b`、`width`和`off`。

### rosbag

&emsp;&emsp;`rosbag`用于数据的保存和复现。
&emsp;&emsp;保存数据的命令如下，执行该命令之后，会在当前目录生成文件`cmd_record.bag`：

``` bash
# “-a”表示保存所有数据，“-O”表示输出，cmd_record是输出文件的名字
$ rosbag record -a -O cmd_record
```

&emsp;&emsp;复现数据的命令如下：

``` bash
$ rosbag play cmd_record.bag
```

### rossrv

&emsp;&emsp;`rossrv`用于查看`service`的相关信息。
&emsp;&emsp;列举当前提供的`service`使用如下命令：

``` bash
$ rossrv list
```

&emsp;&emsp;查看某个类型的`service`所需的请求和响应使用命令`rossrv show`：

``` bash
$ rossrv show std_srvs/Trigger
---
bool success
string message
```

`---`分隔了`request`和`response`，`---`之上是`request`，`---`之下是`response`。

### rosparam

&emsp;&emsp;`rosparam`命令可对`ROS`参数服务器上的参数进行操作。
&emsp;&emsp;`rosparam list`可以列出参数服务器中的所有参数：

``` bash
$ rosparam list
/background_b
/background_g
/background_r
/rosdistro
/rosversion
```

&emsp;&emsp;`rosparam get`可以获得参数的数值：

``` bash
$ rosparam get /background_r
255
```

&emsp;&emsp;`rosparam set`用于设置参数的数值：

``` bash
$ rosparam set /background_r 100
$ rosparam get /background_r
100
```

&emsp;&emsp;`rosparam dump`用于将参数服务器中的参数写入到文件。一般情况下，我们将参数保存在`yaml`文件中：

``` bash
$ rosparam dump param.yaml
```

`param.yaml`的内容如下：

``` bash
background_b: 255
background_g: 255
background_r: 100
rosdistro: 'melodic'
rosversion: '1.14.10'
```

&emsp;&emsp;`rosparam load`用于从文件中加载参数到参数服务器：

``` bash
$ rosparam load param.yaml
```

&emsp;&emsp;`rosparam delete`用于删除参数：

``` bash
$ rosparam delete /background_r
```