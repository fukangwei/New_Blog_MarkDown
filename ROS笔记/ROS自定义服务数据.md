---
title: ROS自定义服务数据
date: 2021-03-14 17:48:14
categories: ROS笔记
---
&emsp;&emsp;在`catkin_ws/src/learning_service`目录下新建一个文件夹`srv`，然后在该文件夹内新建文件`Person.srv`：<!--more-->

``` bash
string name
uint8  age
uint8  sex

uint8 unknown = 0
uint8 male    = 1
uint8 female  = 2
---
string result
```

&emsp;&emsp;在`catkin_ws/src/learning_service/package.xml`中添加功能包依赖：

``` html
<build_depend>message_generation</build_depend>
<exec_depend>message runtime</exec_depend>
```

&emsp;&emsp;在`catkin_ws/src/learning_service/CMakeLists.txt`中添加编译选项：

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
    # LIBRARIES learning_service
    CATKIN_DEPENDS geometry_msgs roscpp rospy std_msgs turtlesim message_runtime
    # DEPENDS system_lib
)
```

&emsp;&emsp;最后编程生成语言相关文件：

``` bash
$ cd ~/catkin_ws
$ catkin_make
```

于是会在`catkin_ws/devel/include/learning_service`目录下生成`PersonRequest.h`和`PersonResponse.h`。
&emsp;&emsp;在`catkin_ws/src/learning_service/src`下新建文件`person_server.cpp`：

``` cpp
/* 该例程将执行“/show_person”服务，服务数据类型“learning_service::Person” */
#include <ros/ros.h>
#include "learning_service/Person.h"

/* service回调函数，输入参数req，输出参数res */
bool personCallback ( learning_service::Person::Request &req,
                      learning_service::Person::Response &res ) {
    /* 显示请求数据 */
    ROS_INFO ( "Person: name:%s  age:%d  sex:%d", req.name.c_str(), req.age, req.sex );
    res.result = "OK"; /* 设置反馈数据 */
    return true;
}

int main ( int argc, char **argv ) {
    ros::init ( argc, argv, "person_server" ); /* ROS节点初始化 */
    ros::NodeHandle n; /* 创建节点句柄 */
    /* 创建一个名为“/show_person”的server，注册回调函数personCallback */
    ros::ServiceServer person_service = n.advertiseService ( "/show_person", personCallback );
    ROS_INFO ( "Ready to show person informtion." );
    ros::spin(); /* 循环等待回调函数 */
    return 0;
}
```

&emsp;&emsp;在`catkin_ws/src/learning_service/src`下新建文件`person_client.cpp`：

``` cpp
/* 该例程将请求“/show_person”服务，服务数据类型“learning_service::Person” */
#include <ros/ros.h>
#include "learning_service/Person.h"

int main ( int argc, char** argv ) {
    ros::init ( argc, argv, "person_client" ); /* 初始化ROS节点 */
    ros::NodeHandle node; /* 创建节点句柄 */
    /* 发现“/spawn”服务后，创建一个服务客户端，连接名为/spawn的service */
    ros::service::waitForService ( "/show_person" );
    ros::ServiceClient person_client = node.serviceClient<learning_service::Person> ( "/show_person" );
    /* 初始化“learning_service::Person”的请求数据 */
    learning_service::Person srv; /* 注意要跟srv的文件名相同 */
    srv.request.name = "Tom";
    srv.request.age  = 20;
    srv.request.sex  = learning_service::Person::Request::male;
    /* 请求服务调用 */
    ROS_INFO ( "Call service to show person[name:%s, age:%d, sex:%d]",
               srv.request.name.c_str(), srv.request.age, srv.request.sex );
    person_client.call ( srv );
    /* 显示服务调用结果 */
    ROS_INFO ( "Show person result : %s", srv.response.result.c_str() );
    return 0;
};
```

&emsp;&emsp;在`catkin_ws/src/learning_service/CMakeLists.txt`中添加如下内容：

``` cmake
add_executable(person_server src/person_server.cpp)
target_link_libraries(person_server ${catkin_LIBRARIES})
add_dependencies(person_server ${PROJECT_NAME}_gencpp)

add_executable(person_client src/person_client.cpp)
target_link_libraries(person_client ${catkin_LIBRARIES})
add_dependencies(person_client ${PROJECT_NAME}_gencpp)
```

&emsp;&emsp;编译并运行代码：

``` bash
$ cd ~/catkin_ws
$ catkin_make
$ source devel/setup.bash
$ roscore
$ rosrun learning_service person_server
$ rosrun learning_service person_client
```

&emsp;&emsp;以上的代码也可以使用`python`来实现。在`catkin_ws/src/learning_service/scripts`目录下，创建一个名为`person_server.py`的文件：

``` python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# 该例程将执行“/show_person”服务，服务数据类型“learning_service::Person”
import rospy
from learning_service.srv import Person, PersonResponse

def personCallback(req):
    # 显示请求数据
    rospy.loginfo("Person: name:%s  age:%d  sex:%d", req.name, req.age, req.sex)
    return PersonResponse("OK") # 反馈数据

def person_server():
    rospy.init_node('person_server') # ROS节点初始化
    # 创建一个名为“/show_person”的server，注册回调函数personCallback
    s = rospy.Service('/show_person', Person, personCallback)
    print "Ready to show person informtion."
    rospy.spin() # 循环等待回调函数

if __name__ == "__main__":
    person_server()
```

&emsp;&emsp;在`catkin_ws/src/learning_service/scripts`目录下，创建一个名为`person_client.py`的文件：

``` python
#!/usr/bin/env python
# -*- coding: utf-8 -*-
# 该例程将请求/show_person服务，服务数据类型learning_service::Person
import sys
import rospy
from learning_service.srv import Person, PersonRequest

def person_client():
    rospy.init_node('person_client') # ROS节点初始化
    # 发现“/spawn”服务后，创建一个服务客户端，连接名为“/spawn”的service
    rospy.wait_for_service('/show_person')

    try:
        person_client = rospy.ServiceProxy('/show_person', Person)
        # 请求服务调用，输入请求数据
        response = person_client("Tom", 20, PersonRequest.male)
        return response.result
    except rospy.ServiceException, e:
        print "Service call failed: %s" % e

if __name__ == "__main__":
    # 服务调用并显示调用结果
    print "Show person result : %s" %(person_client())
```