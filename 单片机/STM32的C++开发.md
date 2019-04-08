---
title: STM32的C++开发
date: 2018-12-29 18:17:31
tags:
---
&emsp;&emsp;`Options for ...` -> `C/C++` -> `Misc Control`, input `--cpp`.
&emsp;&emsp;`led.h`如下所示：

``` cpp
#ifndef LED_H
#define LED_H
​
#include "delay.h"
#include "stm32f10x.h"
​
class Led {
  public:
    Led() {
        ledInit();
    };

    void led_On();
    void led_Off();
    void led_Delay();

  private:
    void ledInit ( void );
    GPIO_InitTypeDef GPIO_InitStruct;
};
​
void Led::ledInit ( void ) {
    GPIO_InitStruct.GPIO_Pin = GPIO_Pin_4;
    RCC_APB2PeriphClockCmd ( RCC_APB2Periph_GPIOC, ENABLE );
    GPIO_InitStruct.GPIO_Speed = GPIO_Speed_50MHz;
    GPIO_InitStruct.GPIO_Mode = GPIO_Mode_Out_PP;
    GPIO_Init ( GPIOC, &GPIO_InitStruct );
}
​
void Led::led_Off() {
    GPIO_SetBits ( GPIOC, GPIO_Pin_4 );
}
​
void Led::led_On() {
    GPIO_ResetBits ( GPIOC, GPIO_Pin_4 );
}
​
void Led::led_Delay() {
    delay_ms ( 500 );
}
​
#endif
```

&emsp;&emsp;`main.c`(或`main.cpp`)如下所示：

``` cpp
#include "led.h"
#include "delay.h"
#include "sys.h"
​
int main ( void ) {
    SystemInit();
    delay_init ( 72 );
    NVIC_Configuration();
    Led led;
​
    while ( 1 ) {
        led.led_On();
        led.led_Delay();
        led.led_Off();
        led.led_Delay();
    }
}
```