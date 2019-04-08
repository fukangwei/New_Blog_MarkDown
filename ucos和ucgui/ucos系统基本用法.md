---
title: ucos系统基本用法
date: 2018-12-29 12:39:06
tags:
---
&emsp;&emsp;在任务中创建任务：

``` c
#define STARTUP_TASK_PRIO     8
#define STARTUP_TASK_STK_SIZE 80
​
void SysTick_init ( void ) {
    SysTick_Config ( SystemCoreClock / OS_TICKS_PER_SEC );
}
​
static OS_STK task_testled[STARTUP_TASK_STK_SIZE];
static OS_STK task_testluart[STARTUP_TASK_STK_SIZE];
​
void TestUart ( void *p_arg ) {
    while ( 1 ) {
        printf ( "hello\r\n" );
        OSTimeDlyHMSM ( 0, 0, 2, 0 );
    }
}
​
void TestLed ( void *p_arg ) {
    OSTaskCreate ( TestUart, ( void * ) 0, &task_testluart[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO );
​
    while ( 1 ) {
        LED0 = !LED0;
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
int main ( void ) {
    SysTick_init();
    LED_Init();
    uart_init ( 9600 );
    OSInit();
    OSTaskCreate ( TestLed, ( void * ) 0, &task_testled[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO - 1 );
    OSStart();
    return 0;
}
```

&emsp;&emsp;创建单次任务：

``` c
#define STARTUP_TASK_PRIO     8
#define STARTUP_TASK_STK_SIZE 80
​
void SysTick_init ( void ) {
    SysTick_Config ( SystemCoreClock / OS_TICKS_PER_SEC );
}
​
static OS_STK task_testled[STARTUP_TASK_STK_SIZE];
static OS_STK task_testluart[STARTUP_TASK_STK_SIZE];
​
void TestUart ( void *p_arg ) {
    printf ( "hello\r\n" );
    OSTaskDel ( OS_PRIO_SELF );
}
​
void TestLed ( void *p_arg ) {
    OSTaskCreate ( TestUart, ( void * ) 0, &task_testluart[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO );
​
    while ( 1 ) {
        LED0 = !LED0;
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
int main ( void ) {
    SysTick_init();
    LED_Init();
    uart_init ( 9600 );
    OSInit();
    OSTaskCreate ( TestLed, ( void * ) 0, &task_testled[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO - 1 );
    OSStart();
    return 0;
}
```

&emsp;&emsp;在创建任务时传递参数：

``` c
void TestUart ( void *p_arg ) {
    printf ( "I get %d\r\n", * ( u8 * ) p_arg );
    OSTaskDel ( OS_PRIO_SELF );
}
​
void TestLed ( void *p_arg ) {
    u8 i = 100;
    OSTaskCreate ( TestUart, ( void * ) &i, &task_testluart[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO );
​
    while ( 1 ) {
        LED0 = !LED0;
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
int main ( void ) {
    SysTick_init();
    LED_Init();
    uart_init ( 9600 );
    OSInit();
    OSTaskCreate ( TestLed, ( void * ) 0, &task_testled[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO - 1 );
    OSStart();
    return 0;
}
```

&emsp;&emsp;使用信号量触发任务：

``` c
static OS_STK task_testled[STARTUP_TASK_STK_SIZE];
static OS_STK task_testluart[STARTUP_TASK_STK_SIZE];
​
OS_EVENT *Sem = NULL; /* 定义信号量指针 */
u8 err = 0;
​
void TestUart ( void *p_arg ) {
    while ( 1 ) {
        OSSemPend ( Sem, 0, &err ); /* 等待信号量 */
        printf ( "hello\r\n" );
        OSTimeDlyHMSM ( 0, 0, 1, 0 );
    }
}
​
void TestLed ( void *p_arg ) {
    while ( 1 ) {
        LED0 = !LED0;
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
        OSSemPost ( Sem ); /* 向串口发送任务发出信号量 */
    }
}
​
int main ( void ) {
    SysTick_init();
    LED_Init();
    uart_init ( 9600 );
    Sem = OSSemCreate ( 1 );
    OSInit();
    OSTaskCreate ( TestLed, ( void * ) 0, &task_testled[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO - 1 );
    OSTaskCreate ( TestUart, ( void * ) 0, &task_testluart[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO );
    OSStart();
    return 0;
}
```

&emsp;&emsp;消息邮箱的基本使用：

``` c
OS_EVENT *Mybox = NULL; /* 定义邮箱指针 */
u8 err = 0;
​
void TestUart ( void *p_arg ) {
    u8 get_Num;
​
    while ( 1 ) {
        get_Num = * ( u8 * ) OSMboxPend ( Mybox, 0, &err );
        printf ( "I get %d\r\n", get_Num );
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
void TestLed ( void *p_arg ) {
    u8 send = 100;
​
    while ( 1 ) {
        LED0 = !LED0;
        OSMboxPost ( Mybox, ( void * ) &send );
        OSTimeDlyHMSM ( 0, 0, 1, 0 );
    }
}
​
int main ( void ) {
    SysTick_init();
    LED_Init();
    uart_init ( 9600 );
    OSInit();
    Mybox = OSMboxCreate ( NULL );
    OSTaskCreate ( TestLed, ( void * ) 0, &task_testled[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO - 1 );
    OSTaskCreate ( TestUart, ( void * ) 0, &task_testluart[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO );
    OSStart();
    return 0;
}
```

&emsp;&emsp;邮箱广播机制：

``` c
static OS_STK task_testled[STARTUP_TASK_STK_SIZE];
static OS_STK task_testluart1[STARTUP_TASK_STK_SIZE];
static OS_STK task_testluart2[STARTUP_TASK_STK_SIZE];
​
OS_EVENT *Mybox = NULL; /* 定义邮箱指针 */
u8 err = 0;
​
void TestUart1 ( void *p_arg ) {
    u8 get_Num;
​
    while ( 1 ) {
        get_Num = * ( u8 * ) OSMboxPend ( Mybox, 0, &err );
        printf ( "Task 1 -- I get %d\r\n", get_Num );
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
void TestUart2 ( void *p_arg ) {
    u8 get_Num;
​
    while ( 1 ) {
        get_Num = * ( u8 * ) OSMboxPend ( Mybox, 0, &err );
        printf ( "Task 2 -- I get %d\r\n", get_Num );
        OSTimeDlyHMSM ( 0, 0, 1, 0 );
    }
}
​
void TestLed ( void *p_arg ) {
    u8 send = 100;
​
    while ( 1 ) {
        LED0 = !LED0;
        OSMboxPostOpt ( Mybox, ( void * ) &send, OS_POST_OPT_BROADCAST ); /* 向所有任务广播消息 */
        OSTimeDlyHMSM ( 0, 0, 1, 0 );
    }
}
​
int main ( void ) {
    SysTick_init();
    LED_Init();
    uart_init ( 9600 );
    OSInit();
    Mybox = OSMboxCreate ( NULL );
    OSTaskCreate ( TestLed, ( void * ) 0, &task_testled[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO - 1 );
    OSTaskCreate ( TestUart1, ( void * ) 0, &task_testluart1[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO );
    OSTaskCreate ( TestUart2, ( void * ) 0, &task_testluart2[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO + 1 );
    OSStart();
    return 0;
}
```

&emsp;&emsp;事件标志组(信号量集)的使用：

``` c
static OS_STK task_testled[STARTUP_TASK_STK_SIZE];
static OS_STK task_testluart1[STARTUP_TASK_STK_SIZE];
static OS_STK task_testluart2[STARTUP_TASK_STK_SIZE];
​
OS_FLAG_GRP *Sem_F = NULL; /* 定义一个信号量集指针 */
u8 err = 0;
​
void TestUart1 ( void *p_arg ) {
    for ( ;; ) {
        OSFlagPost ( Sem_F, ( OS_FLAGS ) 2, OS_FLAG_SET, &err ); /* 向信号量集发信号 */
        printf ( "Uart 1 is running\r\n" );
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
void TestUart2 ( void *p_arg ) {
    for ( ;; ) {
        OSFlagPost ( Sem_F, ( OS_FLAGS ) 1, OS_FLAG_SET, &err ); /* 向信号量集发信号 */
        printf ( "Uart 2 is running\r\n" );
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
void TestLed ( void *p_arg ) {
    for ( ;; ) {
        OSFlagPend ( Sem_F, ( OS_FLAGS ) 3, OS_FLAG_WAIT_SET_ALL, 0, &err ); /* 请求信号量集 */
        LED0 = !LED0;
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
int main ( void ) {
    SysTick_init();
    LED_Init();
    uart_init ( 9600 );
    OSInit();
    Sem_F = OSFlagCreate ( 0, &err );
    OSTaskCreate ( TestLed, ( void * ) 0, &task_testled[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO - 1 );
    OSTaskCreate ( TestUart1, ( void * ) 0, &task_testluart1[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO );
    OSTaskCreate ( TestUart2, ( void * ) 0, &task_testluart2[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO + 1 );
    OSStart();
    return 0;
}
```

&emsp;&emsp;消息队列的使用：

``` c
static OS_STK task_testled[STARTUP_TASK_STK_SIZE];
static OS_STK task_testluart1[STARTUP_TASK_STK_SIZE];
static OS_STK task_testluart2[STARTUP_TASK_STK_SIZE];
​
#define N_MESSAGES    128
void *MsgGrp[N_MESSAGES]; /* 定义消息指针数组 */
OS_EVENT *Str_Q;
u8 err = 0;
​
void TestUart1 ( void *p_arg ) {
    char *recv = NULL;
​
    for ( ;; ) {
        recv = OSQPend ( Str_Q, 0, &err );
        printf ( "Uart1 get %s\r\n", recv );
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
void TestUart2 ( void *p_arg ) {
    char *recv = NULL;
​
    for ( ;; ) {
        recv = OSQPend ( Str_Q, 0, &err );
        printf ( "Uart2 get %s\r\n", recv );
        OSTimeDlyHMSM ( 0, 0, 0, 500 );
    }
}
​
void TestLed ( void *p_arg ) {
    char *send1 = "send_1";
    char *send2 = "send_2";
​
    for ( ;; ) {
        OSQPostFront ( Str_Q, send1 );
        OSQPostFront ( Str_Q, send2 );
        LED0 = !LED0;
        OSTimeDlyHMSM ( 0, 0, 1, 0 );
    }
}
​
int main ( void ) {
    SysTick_init();
    LED_Init();
    uart_init ( 9600 );
    OSInit();
    Str_Q = OSQCreate ( &MsgGrp[0], N_MESSAGES ); /* 创建消息队列 */
    OSTaskCreate ( TestLed, ( void * ) 0, &task_testled[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO - 1 );
    OSTaskCreate ( TestUart1, ( void * ) 0, &task_testluart1[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO );
    OSTaskCreate ( TestUart2, ( void * ) 0, &task_testluart2[STARTUP_TASK_STK_SIZE - 1], STARTUP_TASK_PRIO + 1 );
    OSStart();
    return 0;
}
```