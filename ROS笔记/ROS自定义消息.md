---
title: ROS自定义消息
date: 2021-03-13 18:14:58
categories: ROS笔记
---
&emsp;&emsp;在`ROS`中，如果没有现成的消息类型来描述要去传递的消息，通常我们会使用自定义的消息类型。<!--more-->
&emsp;&emsp;在`catkin_ws/src/learning_topic`目录下新建一个文件夹`msg`，然后在该文件夹内新建文件`Person.msg`：

``` bash
string name
uint8 sex
uint8 age

uint8 unknown = 0
uint8 male    = 1
uint8 female  = 2
```

&emsp;&emsp;在`catkin_ws/src/learning_topic/package.xml`中添加功能包依赖：

``` html
<build_depend>message_generation</build_depend>
<exec_depend>message_runtime</exec_depend>
```

&emsp;&emsp;在`catkin_ws/src/learning_topic/CMakeLists.txt`中添加编译选项：

``` cmake
find_package(catkin REQUIRED COMPONENTS
    geometry_msgs
    roscpp
    rospy
    std_msgs
    turtlesim
    message_generation # 这是新加的
)

add_message_files(FILES Person.msg)
generate_messages(DEPENDENCIES std_msgs)

catkin_package(
    # INCLUDE_DIRS include
    # LIBRARIES learning_topic
    CATKIN_DEPENDS geometry_msgs roscpp rospy std_msgs turtlesim message_runtime
    # DEPENDS system_lib
)
```

&emsp;&emsp;最后编程生成语言相关文件：

``` bash
$ cd ~/catkin_ws
$ catkin_make
```

于是会在`catkin_ws/devel/include/learning_topic`目录下生成一个`Person.h`文件。
&emsp;&emsp;在`catkin_ws/src/learning_topic/src`下新建文件`person_publisher.cpp`：

``` cpp
/* 该例程将发布“/person_info”话题，自定义消息类型“learning_topic::Person” */
#include <ros/ros.h>
#include "learning_topic/Person.h"

int main ( int argc, char **argv ) {
    ros::init ( argc, argv, "person_publisher" ); /* ROS节点初始化 */
    ros::NodeHandle n; /* 创建节点句柄 */
    /* 创建一个Publisher，发布名为“/person_info”的topic，消息类型为“learning_topic::Person”，队列长度10 */
    ros::Publisher person_info_pub = n.advertise<learning_topic::Person> ( "/person_info", 10 );
    ros::Rate loop_rate ( 1 ); /* 设置循环的频率 */

    while ( ros::ok() ) {
        /* 初始化learning_topic::Person类型的消息 */
        learning_topic::Person person_msg;
        person_msg.name = "Tom";
        person_msg.age  = 18;
        person_msg.sex  = learning_topic::Person::male;
        person_info_pub.publish ( person_msg ); /* 发布消息 */
        ROS_INFO ( "Publish Person Info: name:%s  age:%d  sex:%d",
                   person_msg.name.c_str(), person_msg.age, person_msg.sex );
        loop_rate.sleep(); /* 按照循环频率延时 */
    }

    return 0;
}
```

&emsp;&emsp;在`catkin_ws/src/learning_topic/src`下新建文件`person_subscriber.cpp`：

``` cpp
/* 该例程将订阅“/person_info”话题，自定义消息类型“learning_topic::Person” */
#include <ros/ros.h>
#include "learning_topic/Person.h"

/* 接收到订阅的消息后，会进入消息回调函数 */
void personInfoCallback ( const learning_topic::Person::ConstPtr& msg ) {
    /* 将接收到的消息打印出来 */
    ROS_INFO ( "Subcribe Person Info: name:%s  age:%d  sex:%d",
               msg->name.c_str(), msg->age, msg->sex );
}

int main ( int argc, char **argv ) {
    ros::init ( argc, argv, "person_subscriber" ); /* 初始化ROS节点 */
    ros::NodeHandle n; /* 创建节点句柄 */
    /* 创建一个Subscriber，订阅名为“/person_info”的topic，注册回调函数personInfoCallback */
    ros::Subscriber person_info_sub = n.subscribe ( "/person_info", 10, personInfoCallback );
    ros::spin(); /* 循环等待回调函数 */
    return 0;
}
```

&emsp;&emsp;在`catkin_ws/src/learning_topic/CMakeLists.txt`中添加如下内容：

``` cmake
add_executable(person_publisher src/person_publisher.cpp)
target_link_libraries(person_publisher ${catkin_LIBRARIES})
add_dependencies(person_publisher ${PROJECT_NAME}_generate_messages_cpp)

add_executable(person_subscriber src/person_subscriber.cpp)
target_link_libraries(person_subscriber ${catkin_LIBRARIES})
add_dependencies(person_subscriber ${PROJECT_NAME}_generate_messages_cpp)
```

&emsp;&emsp;编译并运行代码：

``` bash
$ cd ~/catkin_ws
$ catkin_make
$ source devel/setup.bash
$ roscore
$ rosrun learning_topic person_publisher
$ rosrun learning_topic person_publisher
```

&emsp;&emsp;以上的代码也可以使用`python`来实现。在`catkin_ws/src/learning_topic/scripts`目录下，创建一个名为`person_publisher.py`的文件：

``` python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# 该例程将发布“/person_info”话题，自定义消息类型“learning_topic::Person”
import rospy
from learning_topic.msg import Person

def velocity_publisher():
    rospy.init_node('person_publisher', anonymous=True) # ROS节点初始化
    # 创建一个Publisher，发布名为“/person_info”的topic，消息类型为“learning_topic::Person”，队列长度10
    person_info_pub = rospy.Publisher('/person_info', Person, queue_size=10)
    rate = rospy.Rate(10) #设置循环的频率

    while not rospy.is_shutdown():
        # 初始化“learning_topic::Person”类型的消息
        person_msg = Person()
        person_msg.name = "Tom"
        person_msg.age  = 18
        person_msg.sex  = Person.male # 发布消息
        person_info_pub.publish(person_msg)
        rospy.loginfo("Publsh person message[%s, %d, %d]",
                person_msg.name, person_msg.age, person_msg.sex)
        rate.sleep() # 按照循环频率延时

if __name__ == '__main__':
    try:
        velocity_publisher()
    except rospy.ROSInterruptException:
        pass
```

&emsp;&emsp;在`catkin_ws/src/learning_topic/scripts`目录下，创建一个名为`person_subscriber.py`的文件：

``` python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# 该例程将订阅“/person_info”话题，自定义消息类型“learning_topic::Person”
import rospy
from learning_topic.msg import Person

def personInfoCallback(msg):
    rospy.loginfo("Subcribe Person Info: name:%s  age:%d  sex:%d",
             msg.name, msg.age, msg.sex)

def person_subscriber():
    rospy.init_node('person_subscriber', anonymous=True) # ROS节点初始化
    # 创建一个Subscriber，订阅名为“/person_info”的topic，注册回调函数personInfoCallback
    rospy.Subscriber("/person_info", Person, personInfoCallback)
    rospy.spin() # 循环等待回调函数

if __name__ == '__main__':
    person_subscriber()
```