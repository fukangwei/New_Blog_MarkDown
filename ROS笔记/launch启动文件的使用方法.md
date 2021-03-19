---
title: launch启动文件的使用方法
date: 2021-03-20 10:50:18
categories: ROS笔记
---
&emsp;&emsp;`launch`文件的作用：通过`XML`文件实现多节点的配置和启动(可自动启动`ROS Master`)。<!--more-->

### launch文件语法

&emsp;&emsp;典型的`launch`文件如下：

``` html
<launch>
    <node pkg="turtlesim" name="sim1" type="turtlesim_node"/>
    <node pkg="turtlesim" name="sim2" type="turtlesim_node"/>
</launch>
```

#### &lt;launch&gt;

&emsp;&emsp;`launch`文件中的根元素采用`<launch>`标签定义。

#### &lt;node&gt;

&emsp;&emsp;启动节点：

``` html
<node pkg="helloworld" type="demo_hello" name="hello" output="screen"/>
```

- `pkg`：节点所在的功能包名称。
- `type`：节点的可执行文件名称。
- `name`：节点运行时的名称。
- `output`：日志的输出目标。

#### &lt;param&gt;和&lt;rosparam&gt;

&emsp;&emsp;设置`ROS`系统运行中的参数，存储在参数服务器中：

``` html
<param name="output_frame" value="odom"/>
```

- `name`：参数名。
- `value`：参数值。

&emsp;&emsp;加载参数文件中的多个参数：

``` html
<rosparam file="params.yaml" command="load" ns="params"/>
```

#### &lt;arg&gt;

&emsp;&emsp;`launch`文件内部的局部变量，仅限于`launch`文件使用：

``` html
<arg name="arg-name" value="arg-value"/>
```

- `name`：参数名。
- `value`：参数值。

调用：

``` html
<param name="foo" value="$(arg arg-name)"/>
<node name="node" pkg="package" type="type" args="$(arg arg-name)"/>
```

#### &lt;remap&gt;

&emsp;&emsp;重映射`ROS`计算图资源的命名：

``` html
<remap from="/turtle1/cmd_vel" to="/cmd_vel"/>
```

- `from`：原命名。
- `to`：映射之后的命名。

#### &lt;include&gt;

&emsp;&emsp;包含其他`launch`文件，类似`C`语言中的头文件包含：

``` html
<include file="$(dirname)/other.launch"/>
```

### launch的用法

#### node的一般用法

&emsp;&emsp;创建功能包：

``` bash
$ cd ~/catkin_ws/src
$ catkin_create_pkg learning_launch
```

在`catkin_ws/src/learning_launch`下新建一个目录`launch`，在该目录中添加文件`sample.launch`：

``` html
<launch>
    <node pkg="learning_topic" type="person_subscriber" name="talker" output="screen"/>
    <node pkg="learning_topic" type="person_publisher" name="listener" output="screen"/>
</launch>
```

&emsp;&emsp;使用如下命令运行代码：

``` bash
$ cd ~/catkin_ws
$ catkin_make
$ source devel/setup.bash
$ roslaunch learning_launch sample.launch
```

#### node的args用法

&emsp;&emsp;在`launch`目录中添加文件`broadcaster_listener.launch`：

``` html
<launch>
    <!-- Turtlesim Node-->
    <node pkg="turtlesim" type="turtlesim_node" name="sim"/>
    <node pkg="turtlesim" type="turtle_teleop_key" name="teleop" output="screen"/>
    <node pkg="learning_tf" type="turtle_tf_broadcaster" args="/turtle1" name="turtle1_tf_broadcaster"/>
    <node pkg="learning_tf" type="turtle_tf_broadcaster" args="/turtle2" name="turtle2_tf_broadcaster"/>
    <node pkg="learning_tf" type="turtle_tf_listener" name="listener"/>
</launch>
```

#### param和rosparam的用法

&emsp;&emsp;在`catkin_ws/src/learning_launch`下新建一个目录`config`，在该目录中添加文件`param.yaml`：

``` yaml
A: 123
B: "hello"

group:
  C: 456
  D: "hello"
```

在`launch`目录中添加文件`turtle_param.launch`：

``` html
<launch>
    <param name="/turtle_number" value="2"/>
    <node pkg="turtlesim" type="turtlesim_node" name="turtlesim_node">
        <param name="turtle_name1" value="Tom"/>
        <param name="turtle_name2" value="Jerry"/>
        <rosparam file="$(find learning_launch)/config/param.yaml" command="load"/>
    </node>
    <node pkg="turtlesim" type="turtle_teleop_key" name="turtle_teleop_key" output="screen"/>
</launch>
```

&emsp;&emsp;使用如下命令运行代码：

``` bash
$ cd ~/catkin_ws
$ catkin_make
$ source devel/setup.bash
$ roslaunch learning_launch turtle_param.launch
```

可以看到小乌龟的界面被打开了，可以用方向键进行控制。
&emsp;&emsp;执行命令`rosparam list`：

``` bash
$ rosparam list
/turtle_number
/turtlesim_node/A
/turtlesim_node/B
/turtlesim_node/group/C
/turtlesim_node/group/D
/turtlesim_node/turtle_name1
/turtlesim_node/turtle_name2
```

&emsp;&emsp;执行命令`rosparam get`：

``` bash
$ rosparam get /turtle_number
2
$ rosparam get /turtlesim_node/turtle_name1
Tom
$ rosparam get /turtlesim_node/turtle_name2
Jerry
```

#### remap的用法

&emsp;&emsp;在`launch`目录中添加文件`remap.launch`：

``` html
<launch>
    <include file="$(find learning_launch)/launch/simple.launch"/>
    <node pkg="turtlesim" type="turtlesim_node" name="turtlesim_node">
        <remap from="/turtle1/cmd_vel" to="/cmd_vel"/>
    </node>
</launch>
```

执行该`launch`文件后，查看`ROS`服务器中的`topic`：

``` bash
$ rostopic list
/cmd_vel
```

可以看出`/turtle1/cmd_vel`被重命名为`/cmd_vel`。