---
title: ROS发布订阅字符串数据
date: 2021-03-20 15:09:39
categories: ROS笔记
---
### C++版本

&emsp;&emsp;`Hello_pub.cpp`如下：<!--more-->

``` cpp
#include "ros/ros.h"
#include "std_msgs/String.h" /* 普通文本类型的消息 */

int main ( int argc, char *argv[] ) {
    setlocale ( LC_ALL, "" ); /* 设置编码 */

    /* 初始化ROS节点，talker是节点名称，需要保证运行后，在ROS网络拓扑中唯一 */
    ros::init ( argc, argv, "talker" );
    ros::NodeHandle nh; /* 实例化ROS句柄 */

    /* 实例化发布者对象。队列中最大保存的消息数为10，超出此阀值时，先进的先销毁(时间早的先销毁) */
    ros::Publisher pub = nh.advertise<std_msgs::String> ( "chatter", 10 );

    std_msgs::String msg;
    msg.data = "你好啊！！！";
    ros::Rate r ( 1 );

    while ( ros::ok() ) {
        pub.publish ( msg );
        ROS_INFO ( "发送的消息：%s", msg.data.c_str() );
        r.sleep();
    }

    return 0;
}
```

&emsp;&emsp;`Hello_sub.cpp`如下：

``` cpp
#include "ros/ros.h"
#include "std_msgs/String.h"

void doMsg ( const std_msgs::String::ConstPtr& msg_p ) {
    ROS_INFO ( "我听见：%s", msg_p->data.c_str() );
}

int main ( int argc, char  *argv[] ) {
    setlocale ( LC_ALL, "" );
    ros::init ( argc, argv, "listener" );
    ros::NodeHandle nh;
    /* 实例化订阅者对象 */
    ros::Subscriber sub = nh.subscribe<std_msgs::String> ( "chatter", 10, doMsg );
    ros::spin(); /* 循环读取接收的数据，并调用回调函数 */
    return 0;
}
```

&emsp;&emsp;在`CMakeLists.txt`中添加编译选项：

``` cmake
add_executable(Hello_pub src/Hello_pub.cpp)
add_executable(Hello_sub src/Hello_sub.cpp)

target_link_libraries(Hello_pub ${catkin_LIBRARIES})
target_link_libraries(Hello_sub ${catkin_LIBRARIES})
```

### python版本

&emsp;&emsp;`talker_p.py`如下：

``` python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import rospy
from std_msgs.msg import String

if __name__ == "__main__":
    rospy.init_node("talker_p")
    pub = rospy.Publisher("chatter", String, queue_size=10)
    msg = String()
    msg_front = "hello 你好"
    count = 0
    rate = rospy.Rate(1)

    while not rospy.is_shutdown():
        msg.data = msg_front + str(count)
        pub.publish(msg)
        rate.sleep()
        rospy.loginfo("写出的数据：%s", msg.data)
        count += 1
```

&emsp;&emsp;`listener_p.py`如下：

``` python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
import rospy
from std_msgs.msg import String

def doMsg(msg):
    rospy.loginfo("接收到的数据：%s", msg.data)

if __name__ == "__main__":
    rospy.init_node("listener_p")
    sub = rospy.Subscriber("chatter", String, doMsg, queue_size=10)
    rospy.spin()
```