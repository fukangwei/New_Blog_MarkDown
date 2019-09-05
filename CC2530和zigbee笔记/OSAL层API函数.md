---
title: OSAL层API函数
date: 2019-02-05 14:35:04
categories: CC2530和zigbee笔记
---
&emsp;&emsp;`OSAL`层提供了很多的`API`来对整个的协议栈进行管理。主要有下面的几类：信息管理、任务同步、时间管理、中断管理、任务管理、内存管理、电源管理以及非易失存储管理。

### 信息管理API

&emsp;&emsp;信息管理为任务间的信息交换或者外部处理事件(例如中断服务程序或一个控制循环内的函数调用)提供一种管理机制。包括允许任务分配或不分配信息缓存、发送命令信息到其他任务、接受应答信息等`API`函数。

#### osal_msg_allocate

&emsp;&emsp;为信息分配缓存空间、任务调用或函数被调用的时候，该空间被信息填充或调用发送信息函数`osal_msg_send`发送缓存空间的信息到其他任务。

``` cpp
byte *osal_msg_allocate ( uint16 len );
```

参数`len`为消息的长度。返回值为指向消息缓冲区的指针，当分配失败时返回`NULL`。注意，不能与函数`osal_mem_alloc`混淆，`osal_mem_alloc`函数被用于为在任务间发送信息分配缓冲区，用该函数也可以分配一个存储区。

#### osal_msg_deallocate

&emsp;&emsp;用于收回缓冲区。

``` cpp
byte osal_msg_deallocate ( byte *msg_ptr );
```

参数`Msg_ptr`为指向将要收回的缓冲区的指针。返回值如下：

- `ZSUCCESS`：回收成功。
- `INVALID_MSG_POINTER`：错误的指针。
- `MSG_BUFFER_NOT_AVAIL`：缓冲区在队列中。

#### osal_msg_send

&emsp;&emsp;任务调用这个函数以实现发送指令或数据给另一个任务或处理单元。目标任务的标识必须是一个有效的系统任务，当调用`osal_create_task`启动一个任务时，将会分配任务标识。`osal_msg_send`也将在目标任务的事件列表中设置`SYS_EVENT_MSG`。

``` cpp
byte osal_msg_send ( byte destination_task, byte *msg_ptr );
```

- `destination_task`：目标任务的标识。
- `msg_ptr`：指向消息缓冲区的指针，必须是`osal_msg_allocate`函数分配的有效的数据缓存。

该函数返回一个字节，指示操作的结果：

- `ZSUCCESS`：消息发送成功。
- `INVALID_MSG_POINTER`：无效指针。
- `INVALID_TASK`：目标任务无效。

#### osal_msg_receive

&emsp;&emsp;任务调用这个函数来接收消息。消息处理完毕后，发送消息的任务必须调用`osal_msg_deallocate`收回缓冲区。一个任务接收一个命令信息时，调用该函数。

``` cpp
byte *osal_msg_receive ( byte task_id );
```

参数`task_id`为消息发送者的任务标识。返回值为指向消息所存放的缓冲区指针，如果没有收到消息将返回`NULL`。

### 同步任务API

&emsp;&emsp;这个`API`使能一个任务等待一个事件的发生和返回控制，而不是一直等待。在这个`API`中的函数可以用来为任务设置事件，立刻通知任务有事件被设置。

#### osal_set_event

&emsp;&emsp;函数用来设置一个任务的事件标志。

``` cpp
byte osal_set_event ( byte task_id, UINT16 event_flag );
```

- `task_id`：任务标识。
- `event_flag`：`2`个字节的位图，每个位特指一个事件。只有一个系统事件(`SYS_EVENT_MSG`)，其他事件在接收任务中定义。

返回值如下：

- `ZSUCCESS`：成功设置。
- `INVALID_TASK`：无效任务。

### 时间管理API

&emsp;&emsp;这个`API`允许内部任务(`Z-Stack`)以及应用层任务使用定时器。函数提供了启动和停止定时器的功能，定时器最小增量为`1ms`。

#### osal_start_timer

&emsp;&emsp;启动一个定时器，当定时器终止时，指定的事件标志位被设置。通过在任务中调用`osal_start_timer`函数设置时间标志位。如果指明任务`ID`，则可以用`osal_start_timerEx`函数替代`osal_start_timer`。

``` cpp
byte osal_start_timer ( UINT16 event_id, UINT16 timeout_value );
```

- `event_id`：用户定义的事件标志位`event bit`，当定时器到点时，事件将通知任务。
- `timeout_value`：定时值(`ms`)。

返回值如下：

- `ZSUCCESS`：`Timer`成功开启。
- `NO_TIMER_AVAILABLE`：无法开启。

#### osal_start_timerEx

&emsp;&emsp;功能与`osal_start_timer`相近，只不过参数多了一个任务`ID`，这个函数允许调用者为另一个任务启动定时器。

``` cpp
byte osal_start_timerEx ( byte taskID, UINT16 event_id, UINT16 timeout_value );
```

- `taskID`：当定时器终止时，得到该事件的任务`ID`。
- `event_id`：用户定义的事件位，当定时器终止时，正在调用的任务将被通报。
- `timeout_value`：定时器事件被设置之前时间的计数。

返回值如下：

- `ZSUCCESS`：`Timer`成功开启。
- `NO_TIMER_AVAILABLE`：无法开启。

#### osal_stop_timer

&emsp;&emsp;停止正在运行的定时器，停止外部事件调用`osal_stop_timerEx`，可以停止不同任务的定时器。

``` cpp
byte osal_stop_timer ( UINT16 event_id );
```

参数`event_id`为将要结束的目标事件(该事件是启动定时器的事件)定时器的标识符。返回值如下：

- `ZSUCCESS`：`Timer`成功停止。
- `INVALID_EVENT_ID`：无效事件。

#### osal_stop_timerEx

&emsp;&emsp;结束外部事件的定时器，指明了任务的`ID`。

``` cpp
byte osal_stop_timerEx ( byte task_id, UINT16 event_id );
```

- `task_id`：停止定时器所在的任务`ID`。
- `event_id`：被停止定时器的标识符。

返回值如下：

- `ZSUCCESS`：`Timer`成功停止。
- `INVALID_EVENT_ID`：无效事件。

#### osal_GetSystemClock

&emsp;&emsp;读取系统时间。

``` cpp
uint32 osal_GetSystemClock ( void );
```

返回值为系统时间(`ms`)。

#### osal_start_reload_timer

&emsp;&emsp;设置定时器，与`osal_stop_timerEx`不同的是该函数设置的定时器超时后被重新装载。

``` cpp
uint8 osal_start_reload_timer ( uint8 taskID, uint16 event_id, uint16 timeout_value );
```

返回值如下：

- `ZSUCCESS`：`Timer`成功开启。
- `NO_TIMER_AVAILABLE`：无法开启。

### 中断管理API

&emsp;&emsp;这些`API`实现任务与外部中断的接口，函数允许一个任务关联每一个具体的中断程序程序，可以开关中断。在中断服务程序内，其他任务可以设置事件。

#### osal_int_enable

&emsp;&emsp;函数用于使能中断。一旦允许，中断发生时将引起中断分配的服务程序运行。

``` cpp
byte osal_int_enable ( byte interrupt_id );
```

参数`interrupt_id`为被允许的中断的标识符。返回值如下：

- `ZSUCCESS`：`Interrupt`成功使能。
- `INVALID_INTERRUPT_ID`：无效中断。

#### osal_int_disable

&emsp;&emsp;关闭中断。

``` cpp
byte osal_int_disable ( byte interrupt_id );
```

参数`interrupt_id`为被禁止中断的标识符。返回值如下：

- `ZSUCCESS`：`Interrupt`成功关闭。
- `INVALID_INTERRUPT_ID`：无效中断。

### 任务管理API

&emsp;&emsp;该`API`用在添加和管理`OSAL`中的任务。每一个任务由任务初始化函数和时间处理函数组成。

#### osal_init_system

&emsp;&emsp;该函数初始化`OSAL`系统。该函数必须在启动任何一个`OSAL`函数之前被调用。

``` cpp
byte osal_init_system ( void );
```

返回值若为`ZSUCCESS`表示成功。

#### osal_start_system

&emsp;&emsp;这个函数是系统任务的主循环函数，在循环里面将遍历所有的任务事件，为触发事件的任务调用任务事件处理函数。如果一个特定任务有事件发送，那么该函数就将调用该任务的事件处理函数。当事件处理完之后，将返回主循环，继续查找其他的任务事件。如果没有事件，函数将把处理器转到睡眠模式。

``` cpp
void osal_start_system ( void );
```

#### osal_self

&emsp;&emsp;这个函数返回正在被调用任务的任务标识符。如果在一个中断服务子程序中调用该函数，将返回一个错误结果。

``` cpp
byte osal_self ( void );
```

返回值为当前活动的任务的任务标识符。

#### osalTaskAdd

&emsp;&emsp;这个函数添加一个任务到任务系统中，一个任务由两个函数组成，即初始化函数与信息处理函数。

``` cpp
/* 任务初始化函数原型 */
typedef void ( *pTaskInitFn ) ( unsigned char task_id );
/* 事件句柄函数原型 */
typedef unsigned short ( *pTaskEventHandlerFn ) ( unsigned char task_id, unsigned short event );
/* 添加任务函数原型 */
void osalTaskAdd ( const pTaskInitFn pfnInit, const pTaskEventHandlerFn, \
                   pfnEventProcessor, const byte taskPriority );
```

- `pfnInit`：指向任务初始化函数的指针。
- `pfnEventProcessor`：指向任务事件处理器函数的指针。
- `taskPriority`：任务的优先级，值为`0`至`255`，常用选项如下：

优先级                    | 值
--------------------------|-----
`OSAL_TASK_PRIORITY_LOW`  | `50`
`OSAL_TASK_PRIORITY_MED`  | `130`
`OSAL_TASK_PRIORITY_HIGH` | `230`

### 内存管理API

&emsp;&emsp;该`API`呈现一个简单的内存分配系统，这些函数允许动态内存分配。

#### osal_mem_alloc

&emsp;&emsp;这个函数是一个简单内存分配函数，如果成功则返回一个缓冲区的指针。

``` cpp
void *osal_mem_alloc ( uint16 size );
```

参数`size`为缓冲区的大小。该函数返回一个`void`指针指向新分配的缓冲区，如果没有足够的内存来分配，则返回`NULL`。

#### osal_mem_free

&emsp;&emsp;这个函数释放已分配的内存来重新使用。只有当内存已使用`osal_mem_alloc`分配过才可以工作。

``` cpp
void osal_mem_free ( void *ptr );
```

参数`ptr`是指向将被释放的缓冲区的指针，这个缓冲区必须之前被`osal_mem_alloc`分配过。

### 电源管理API

&emsp;&emsp;这里的函数描述了`OSAL`的电源管理系统，当`OSAL`安全地关闭接收器与外部硬件，并使处理器进入休眠模式时，该系统提供向应用或任务通报该事件的方法。

#### osal_pwrmgr_task_state

&emsp;&emsp;该函数被每个任务调用，声明该任务是否需要节能。任务被创建时，默认是节能模式。如果任务总是需要节能，那么就不需要调用该函数。

``` cpp
byte osal_pwrmgr_task_state ( byte task_id, pwrmgr_state_t state );
```

参数`state`可以改变任务的电源状态：

类型              | 描述
------------------|------------------
`PWRMGR_CONSERVE` | 打开节能模式，所有任务都须一致，为任务初始化的缺省模式
`PWRMGR_HOLD`     | 关闭节能模式

返回值显示操作的结果：

- `ZSUCCESS`：成功。
- `INVALID_TASK`：无效任务。

#### osal_pwrmgr_device

&emsp;&emsp;该函数在上电或电源需求变更时调用(例如电源支持协调器)。

``` cpp
void osal_pwrmgr_state ( byte pwrmgr_device );
```

参数`pwrmgr_device`用于更改或设置节电模式：

- `PWRMGR_ALWAYS_ON`：无节电。
- `PWRMGR_BATTERY`：开节电。

### 非易失性(NV)存储管理

&emsp;&emsp;这部分讲述`OSAL`的`NV`非易失性(`NV`)存储系统，该系统提供了一种方式来为应用永久存放信息到设备内存中。它也可以用于某些堆栈条目的固定存储，这些`NV`函数被设计用来读取/写入用户定义的由任何数据类型组成的(如结构与数组)项目。用户可以通过设定适当的偏移量与长度读取或写入一个完整的项目或项目中的一个单元。该`API`为`NV`存储介质独有，与存储体本身没有关系。可以被`flash`或`eeprom`使用。
&emsp;&emsp;每个`NV`项目都有一个惟一的标识符，每个应用都有特定的`ID`值范围。如果你的应用创建了自己的`NV`项目，则必须从应用值范围内选一个`ID`：

选项              | 说明
------------------|-----
`0x0000`          | `Reserved`
`0x0001 - 0x0020` | `OSAL`
`0x0021 - 0x0040` | `NWK`
`0x0041 - 0x0060` | `APS`
`0x0061 - 0x0080` | `Security`
`0x0081 - 0x00A0` | `ZDO`
`0x00A1 - 0x0200` | `Reserved`
`0x0201 - 0x0FFF` | `Application`
`0x1000 - 0xFFFF` | `Reserved`

#### osal_nv_item_init

&emsp;&emsp;初始化`NV`中的一条项目。这个函数检测`NV`项目的存在与否，如果不存在，则创建一个`NV`项目。在调用`osal_nv_read`或`osal_nv_write`前，每一个项目必须先调用该函数。

``` cpp
byte osal_nv_item_init ( uint16 id, uint16 len, void *buf );
```

- `id`：用户定义项目标识符。
- `len`：项目长度(字节)。
- `buf`：项目初始化数据指针，如果没有初始化数据，则设为`NULL`。

返回值显示操作的结果：

- `ZSUCCESS`：成功。
- `NV_ITEM_UNINIT`：成功但项目不存在。

#### osal_nv_read

&emsp;&emsp;读取`NV`数据，这个函数被用来读取整个`NV`项目或其中一个项目，读取的数据被复制到`buf`中。

``` cpp
byte osal_nv_read ( uint16 id, uint16 offset, uint16 len, void *buf );
```

- `id`：用户定义项目标识符。
- `offset`：项目的内存偏移量(字节)。
- `len`：项目长度(字节)。
- `buf`：数据读取到该缓冲区。

返回值显示操作的结果：

- `ZSUCCESS`：成功。
- `NV_ITEM_UNINIT`：项目没有初始化。
- `NV_OPER_FAILED`：操作失败。

#### nv_osal_write

&emsp;&emsp;写入数据到`NV`中，这个函数被用来写入整个`NV`项目或一个项目中的单元(通过一个偏移量来索引该项目)。

``` cpp
byte osal_nv_write ( uint16 id, uint16 offset, uint16 len, void *buf );
```

- `id`：用户定义项目标识符。
- `offset`：项目的内存偏移量(字节)。
- `len`：项目长度(字节)。
- `buf`：要写入的数据。

返回值显示操作的结果：

- `ZSUCCESS`：成功。
- `NV_ITEM_UNINIT`：项目没有初始化。
- `NV_OPER_FAILED`：操作失败。

#### osal_offsetof

&emsp;&emsp;该宏在一个结构中按字节计算一个单元的内存偏移量，它被`NV`的`API`函数用来计算偏移量。

``` cpp
#define osal_offsetof(type, member) ((uint16) &(((type *) 0)->member))
```

- `type`：结构类型。
- `member`：结构成员。