---
title: FreeRTOSConfig配置
date: 2021-08-03 22:24:49
categories: FreeRTOS笔记
---
&emsp;&emsp;`FreeRTOS`的配置是通过`FreeRTOSConfig.h`中的宏定义实现的。<!--more-->

### INCLUDE_vTaskDelay

&emsp;&emsp;如果要使用函数`vTaskDelay`，则需要将`INCLUDE_vTaskDelay`定义为`1`。

### INCLUDE_vTaskDelete

&emsp;&emsp;如果要使用函数`vTaskDelete`，则需要将`INCLUDE_vTaskDelete`定义为`1`。

### INCLUDE_xTaskResumeFromISR

&emsp;&emsp;如果要使用函数`xTaskResumeFromISR`，则需要将`INCLUDE_xTaskResumeFromISR`和`INCLUDE_vTaskSuspend`都定义为`1`。

### INCLUDE_vTaskSuspend

&emsp;&emsp;如果要使用函数`vTaskSuspend`、`vTaskResume`、`prvTaskIsTaskSuspended`、`xTaskResumeFromISR`，则需要将`INCLUDE_vTaskSuspend`定义为`1`。
&emsp;&emsp;如果要使用函数`xTaskResumeFromISR`，则需要将`INCLUDE_xTaskResumeFromISR`和`INCLUDE_vTaskSuspend`都定义为`1`。

### configASSERT

&emsp;&emsp;类似于`C`标准库中的`assert`函数，用于检查传入的参数是否合理：

``` cpp
#define vAssertCalled(char, int)  printf( "Error: %s, %d\r\n", char, int )
#define configASSERT(x)           if( ( x ) == 0 ) vAssertCalled( __FILE__, __LINE__ )
```

当调试完成以后，尽量关闭`configASSERT`，防止增加开销。

### configCPU_CLOCK_HZ

&emsp;&emsp;需要将`configCPU_CLOCK_HZ`设置为`CPU`的频率。如果`STM32`的频率为`72MHz`，那么：

``` cpp
#define configCPU_CLOCK_HZ  ( ( unsigned long ) 72000000 )
```

### configMAX_PRIORITIES

&emsp;&emsp;设置任务的优先级数量，其中`0`是最低的优先级，`configMAX_PRIORITIES - 1`是最高的优先级。

### configTICK_RATE_HZ

&emsp;&emsp;用于设置`FreeRTOS`的系统时钟节拍频率。如果将此宏设置为`1000`，则系统时钟每`1ms`发生一次中断。

### configUSE_IDLE_HOOK

&emsp;&emsp;设置为`1`时，表示使用空闲任务钩子函数，其函数原型如下：

``` cpp
void vApplicationIdleHook ( void );
```

### configUSE_COUNTING_SEMAPHORES

&emsp;&emsp;设置为`1`时，表示启用计数型信号量。

### configUSE_MUTEXES

&emsp;&emsp;设置为`1`时，表示使用互斥信号量。

### configUSE_STATS_FORMATTING_FUNCTIONS

&emsp;&emsp;`configUSE_TRACE_FACILITY`和`configUSE_STATS_FORMATTING_FUNCTIONS`都为`1`时，可以使用`vTaskList`和`vTaskGetRunTimeStats`。

### configUSE_TICKLESS_IDLE

&emsp;&emsp;设置为`1`时，使能低功耗`tickless`模式。

### configUSE_TIMERS

&emsp;&emsp;设置为`1`时，使能软件定时器功能。
&emsp;&emsp;当宏`configUSE_TIMERS`为`1`时，宏`configTIMER_TASK_PRIORITY`、`configTIMER_QUEUE_LENGTH`和`configTIMER_TASK_STACK_DEPTH`必须被设置。

### configLIBRARY_LOWEST_INTERRUPT_PRIORITY

&emsp;&emsp;用于设置最低的中断优先级，此处设置为`15`。
&emsp;&emsp;`STM32`的`NVIC`被设置为`NVIC_PriorityGroup_4`，所有`4`位都是抢占优先级，因此最低的中断优先级就是`15`。

### configLIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY

&emsp;&emsp;用于设置`FreeRTOS`系统可管理的最高的中断优先级。
&emsp;&emsp;这里设置为`5`，表示用户可以在抢占式优先级为`5`到`15`的中断里调用`FreeRTOS`的`API`函数。

### configMAX_SYSCALL_INTERRUPT_PRIORITY

&emsp;&emsp;此宏是由`configLIBRARY_MAX_SYSCALL_INTERRUPT_PRIORITY`左移`4`位而来的。

- 低于此优先级的中断可以安全地调用 FreeRTOS 的 API 函数。
- 高于此优先级的中断 FreeRTOS 是不能禁止的，中断服务函数也不能调用 FreeRTOS 的 API 函数