---
title: 协议栈移植DHT11
categories: CC2530和zigbee笔记
date: 2019-02-05 13:50:18
---
&emsp;&emsp;终端设备读取`DHT11`温湿度信息，通过点播方式发送到协调器，协调器通过串口打印出来。使用点播的原因是终端设备有针对性地发送数据给指定设备，而广播和组播可能会造成数据冗余。<!--more-->
&emsp;&emsp;首先将裸机程序里面的`DHT11.c`和`DHT11.h`文件复制到`\zstack\Samples\SampleApp\Source`文件夹下面，在协议栈的`APP`目录树下添加`DHT11.c`文件。整个实验以点播为依托，所以我们的实验也是在点播例程的基础上完成。
&emsp;&emsp;编程是在`SAMPLEAPP.c`上进行的，首先包含`DHT11.h`头文件，在`SampleApp_Init`函数中添加如下代码：

``` cpp
P0SEL &= 0xbf; /* 温湿度传感器使用P0.6引脚 */
```

借用周期性点播函数，`1s`读取温度传感器`1`次，通过液晶显示和串口打印，并实现点对点地发送至协调器：

``` cpp
if ( events & SAMPLEAPP_SEND_PERIODIC_MSG_EVT ) {
    uint8 T[8]; /* 温度 + 提示符 */
    DHT11(); /* 温度检测 */
    T[0] = wendu_shi + 48;
    T[1] = wendu_ge + 48;
    T[2] = ' ';
    T[3] = shidu_shi + 48;
    T[4] = shidu_ge + 48;
    T[5] = ' ';
    T[6] = ' ';
    T[7] = ' ';
    /* 串口打印 */
    HalUARTWrite ( 0, "temp = ", 5 );
    HalUARTWrite ( 0, T, 2 );
    HalUARTWrite ( 0, "\n", 1 );
    HalUARTWrite ( 0, "humidity = ", 9 );
    HalUARTWrite ( 0, T + 3, 2 );
    HalUARTWrite ( 0, "\n", 1 );
    /* LCD显示 */
    HalLcdWriteString ( "Temp: humidity:", HAL_LCD_LINE_3 ); /* LCD显示 */
    HalLcdWriteString ( T, HAL_LCD_LINE_4 ); /* LCD显示 */
    SampleApp_SendPointToPointMessage(); /* 点播函数 */
    /* Setup to send message again in normal period (+ a little jitter) */
    osal_start_timerEx ( SampleApp_TaskID, SAMPLEAPP_SEND_PERIODIC_MSG_EVT, \
                       ( SAMPLEAPP_SEND_PERIODIC_MSG_TIMEOUT + ( osal_rand() & 0x00FF ) ) );
    return ( events ^ SAMPLEAPP_SEND_PERIODIC_MSG_EVT ); /* return unprocessed events */
}
```

打开`DHT11.c`文件，将原来的延时函数改成协议栈自带的延时函数，保证时序的正确：

``` cpp
void Delay_us() { /* 1us延时 */
    MicroWait ( 1 );
}

void Delay_10us() { /* 10us延时 */
    MicroWait ( 10 );
}
```

同时要包含头文件`OnBoard.h`。在`EndDevice`的点播发送函数中将温度信息发送出去：

``` cpp
void SampleApp_SendPointToPointMessage ( void ) {
    uint8 T_H[4]; /* 温湿度 */
    T_H[0] = wendu_shi + 48;
    T_H[1] = wendu_ge % 10 + 48;
    T_H[2] = shidu_shi + 48;
    T_H[3] = shidu_ge % 10 + 48;

    if ( AF_DataRequest (
            &Point_To_Point_DstAddr,
            &SampleApp_epDesc,
            SAMPLEAPP_POINT_TO_POINT_CLUSTERID,
            4,
            T_H,
            &SampleApp_TransID,
            AF_DISCV_ROUTE,
            AF_DEFAULT_RADIUS ) == afStatus_SUCCESS ) {
    } else {
        /* Error occurred in request to send */
    }
}
```

&emsp;&emsp;协调器代码如下：

``` cpp
void SampleApp_MessageMSGCB ( afIncomingMSGPacket_t *pkt ) {
    uint16 flashTime;

    switch ( pkt->clusterId ) {
        case SAMPLEAPP_POINT_TO_POINT_CLUSTERID:
            /* 温度打印 */
            HalUARTWrite ( 0, "Temp is:", 8 ); /* 提示接收到数据 */
            HalUARTWrite ( 0, &pkt->cmd.Data[0], 2 ); /* 温度 */
            HalUARTWrite ( 0, "\n", 1 ); /* 回车换行 */
            /* 湿度打印 */
            HalUARTWrite ( 0, "Humidity is:", 12 ); /* 提示接收到数据 */
            HalUARTWrite ( 0, &pkt->cmd.Data[2], 2 ); /* 湿度 */
            HalUARTWrite ( 0, "\n", 1 ); /* 回车换行 */
            break;
        case SAMPLEAPP_FLASH_CLUSTERID:
            flashTime = BUILD_UINT16 ( pkt->cmd.Data[1], pkt->cmd.Data[2] );
            HalLedBlink ( HAL_LED_4, 4, 50, ( flashTime / 4 ) );
            break;
    }
}
```