---
title: 协议栈NV操作
date: 2019-02-05 13:11:58
tags:
---
&emsp;&emsp;1. 配置串口：

``` cpp
void UartInit ( halUARTCBack_t SerialCallBack ) {
    halUARTCfg_t uartConfig;
    /* configure UART */
    uartConfig.configured           = TRUE;
    uartConfig.baudRate             = HAL_UART_BR_115200;
    uartConfig.flowControl          = FALSE;
    uartConfig.flowControlThreshold = 128;
    uartConfig.rx.maxBufSize        = 128;
    uartConfig.tx.maxBufSize        = 28;
    uartConfig.idleTimeout          = 6;
    uartConfig.intEnable            = TRUE;
    uartConfig.callBackFunc         = SerialCallBack;
    /* Note: Assumes no issue opening UART port. */
    ( void ) HalUARTOpen ( 0, &uartConfig );
    return;
}
```

&emsp;&emsp;2. 设置`NV`条目的`ID`号：

``` cpp
#define TEST_NV 0x0201
```

&emsp;&emsp;3. 设置串口的回调函数：

``` cpp
void SerialCallback ( uint8 port, uint8 events ) {
    uint8 value_read;
    uint8 value = 18;
    uint8 uart_buf[2];
    uint8 cmd[6];
    HalUARTRead ( 0, cmd, 6 );
​
    if ( osal_memcmp ( cmd, "nvread", 6 ) ) {
        osal_nv_item_init ( TEST_NV, 1, NULL );
        osal_nv_write ( TEST_NV, 0, 1, &value );
        osal_nv_read ( TEST_NV, 0, 1, &value_read );
        uart_buf[0] = value_read / 10 + '0';
        uart_buf[1] = value_read % 10 + '0';
        HalUARTWrite ( 0, uart_buf, 2 );
    }
}
```

&emsp;&emsp;4. 在`SampleApp_Init`函数中对串口进行设置：

``` cpp
UartInit ( SerialCallback );
```

不要忘记添加头文件`OSAL_Nv.h`。
&emsp;&emsp;最后下载代码，对其进行测试：连接串口，通过串口输入字符串`nvread`，串口就会回复`18`，测试成功。