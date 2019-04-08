---
title: Contiki和uip使用注意
date: 2019-02-04 14:27:20
tags:
---
1. 在`Contiki`操作系统中，延时函数要尽量使用软件延时的方法，不要使用利用了`Systick`定时器制作的精确延时函数。因为`Contiki`也需要`Systick`定时器作为时基。
2. 在`Contiki`操作系统中，不要出现局部自动变量(`auto`)，要将其改为局部静态变量(`static`)。
3. 在`Contiki`操作系统中，系统应用的初始化例如看门狗的初始化尽量放在主函数中，设备驱动的初始化尽量放在创建的任务中。
4. 用`keil`开发`Contiki`操作系统时，如果在别的源文件中有`PROCESS_THREAD`任务，在`main.c`文件中也可以用`AUTOSTART_PROCESSES`进行调用。
5. 增大`uip`数据包的缓冲区可以提高`WebServer`的响应速度(在`contiki`系统中，设置`contiki-conf.h`文件中的`UIP_CONF_BUFFER_SIZE`)。但不能设置太大，不能超过`1500`。
6. 不要轻易地修改别人的源文件，出现错误或警告有可能是头文件的配置或编译器的配置出了问题，例如`uip`的移植。
7. 在`Contiki`操作系统中，一个源文件要想调用其他源文件中的任务，可以在当前源文件中加入语句`PROCESS_NAME(任务名);`。
8. `contiki`中`led`的`coap`控制负载为`&color=r&mode=on`，一定不要忘记`&`。