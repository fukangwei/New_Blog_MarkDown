---
title: ROS的参数使用与编程
date: 2021-03-14 21:29:11
categories: ROS笔记
---
&emsp;&emsp;参数模型如下所示：<!--more-->

<img src="./ROS的参数使用与编程/参数模型.png" width="50%">

&emsp;&emsp;获取和设置参数的步骤如下：

1. 初始化`ROS`节点。
2. `get`函数获取参数。
3. `set`函数设置参数。

&emsp;&emsp;我们仍以小乌龟为例，创建一个`package`。具体步骤如下：

``` bash
$ cd ~/catkin_ws/src
$ catkin_create_pkg learning_paramater roscpp rospy std_srvs
```

&emsp;&emsp;在目录`catkin_ws/src/learning_paramater`下创建一个名为`parameter_config.cpp`的文件：

``` cpp
/* 该例程设置/读取海龟例程中的参数 */
#include <string>
#include <ros/ros.h>
#include <std_srvs/Empty.h>

int main ( int argc, char **argv ) {
    int red, green, blue;
    ros::init ( argc, argv, "parameter_config" ); /* ROS节点初始化 */
    ros::NodeHandle node; /* 创建节点句柄 */
    /* 读取背景颜色参数 */
    ros::param::get ( "/background_r", red );
    ros::param::get ( "/background_g", green );
    ros::param::get ( "/background_b", blue );
    ROS_INFO ( "Get Backgroud Color[%d, %d, %d]", red, green, blue );
    /* 设置背景颜色参数 */
    ros::param::set ( "/background_r", 255 );
    ros::param::set ( "/background_g", 255 );
    ros::param::set ( "/background_b", 255 );
    ROS_INFO ( "Set Backgroud Color[255, 255, 255]" );
    /* 读取背景颜色参数 */
    ros::param::get ( "/background_r", red );
    ros::param::get ( "/background_g", green );
    ros::param::get ( "/background_b", blue );
    ROS_INFO ( "Re-get Backgroud Color[%d, %d, %d]", red, green, blue );
    /* 调用服务，刷新背景颜色 */
    ros::service::waitForService ( "/clear" );
    ros::ServiceClient clear_background = node.serviceClient<std_srvs::Empty> ( "/clear" );
    std_srvs::Empty srv;
    clear_background.call ( srv );
    sleep ( 1 );
    return 0;
}
```

&emsp;&emsp;在`catkin_ws/src/learning_paramater/CMakeLists.txt`中添加如下内容：

``` cmake
add_executable(parameter_config src/parameter_config.cpp)
target_link_libraries(parameter_config ${catkin_LIBRARIES})
```

&emsp;&emsp;编译并运行代码：

``` bash
$ cd ~/catkin_ws
$ catkin_make
$ source devel/setup.bash
$ roscore
$ rosrun learning_paramater parameter_config
```

&emsp;&emsp;以上的代码也可以使用`python`来实现。在`catkin_ws/src/learning_paramater/scripts`目录下，创建一个名为`parameter_config.py`的文件：

``` python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# 该例程设置/读取海龟例程中的参数
import sys
import rospy
from std_srvs.srv import Empty

def parameter_config():
    rospy.init_node('parameter_config', anonymous=True) # ROS节点初始化
    # 读取背景颜色参数
    red   = rospy.get_param('/background_r')
    green = rospy.get_param('/background_g')
    blue  = rospy.get_param('/background_b')
    rospy.loginfo("Get Backgroud Color[%d, %d, %d]", red, green, blue)
    # 设置背景颜色参数
    rospy.set_param("/background_r", 255)
    rospy.set_param("/background_g", 255)
    rospy.set_param("/background_b", 255)
    rospy.loginfo("Set Backgroud Color[255, 255, 255]")
    # 读取背景颜色参数
    red   = rospy.get_param('/background_r')
    green = rospy.get_param('/background_g')
    blue  = rospy.get_param('/background_b')
    rospy.loginfo("Get Backgroud Color[%d, %d, %d]", red, green, blue)
    # 发现“/spawn”服务后，创建一个服务客户端，连接名为“/spawn”的service
    rospy.wait_for_service('/clear')

    try:
        clear_background = rospy.ServiceProxy('/clear', Empty)
        # 请求服务调用，输入请求数据
        response = clear_background()
        return response
    except rospy.ServiceException, e:
        print "Service call failed: %s" % e

if __name__ == "__main__":
    parameter_config()
```