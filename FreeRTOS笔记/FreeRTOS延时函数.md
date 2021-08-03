---
title: FreeRTOS延时函数
date: 2021-08-04 06:15:00
categories: FreeRTOS笔记
---
### vTaskDelay

&emsp;&emsp;函数原型如下，`xTicksToDelay`是要延时的节拍数：<!--more-->

``` cpp
void vTaskDelay ( const TickType_t xTicksToDelay );
```

&emsp;&emsp;如果`configTICK_RATE_HZ`设置为`1000`，那么`vTaskDelay ( 1000 );`将延时`1s`。