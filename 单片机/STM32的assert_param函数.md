---
title: STM32的assert_param函数
categories: 单片机
abbrlink: 753412ab
date: 2019-07-05 06:41:26
---
&emsp;&emsp;我们在分析库函数时，几乎每一个函数的实现都有`assert_param`。下面以`assert_param(IS_GPIO_ALL_PERIPH(GPIOx));`为例说一下我的理解。函数的参数是`IS_GPIO_ALL_PERIPH(GPIOx)`，可以寻找到其原型：<!--more-->

``` cpp
#define IS_GPIO_ALL_PERIPH(PERIPH) (((*(uint32_t*)&(PERIPH)) == GPIOA_BASE) || \
                                    ((*(uint32_t*)&(PERIPH)) == GPIOB_BASE) || \
                                    ((*(uint32_t*)&(PERIPH)) == GPIOC_BASE) || \
                                    ((*(uint32_t*)&(PERIPH)) == GPIOD_BASE) || \
                                    ((*(uint32_t*)&(PERIPH)) == GPIOE_BASE) || \
                                    ((*(uint32_t*)&(PERIPH)) == GPIOF_BASE) || \
                                    ((*(uint32_t*)&(PERIPH)) == GPIOG_BASE))
```

这个宏定义的作用就是检查参数`PERIPH`，判断参数`PERIPH`是否为`GPIOX`基址中的一个。只要有一个为真，则其值为真，否则为假。下面再回到`assert_param`这个函数，在`stm32f10x_conf.h`中找到原型：

``` cpp
#ifdef USE_FULL_ASSERT
    #define assert_param(expr) ((expr) ? (void) 0 : assert_failed((uint8_t*)__FILE__, __LINE__))
    void assert_failed ( uint8_t *file, uint32_t line );
#else
    #define assert_param(expr) ((void) 0)
#endif
```

这是一个预编译文件，若是在这个文件中定义了`USE_FULL_ASSERT`，则执行后面的语句。我们在程序中一般都没什么定义，即执行后面这个语句`((void) 0)`。
&emsp;&emsp;若是定义了`USE_FULL_ASSERT`，就调用函数`assert_param`对参数`IS_GPIO_ALL_PERIPH(GPIOx)`的正确性进行检查。若是返回`1`，就执行语句`(void) 0`；若是返回`0`，则执行函数`assert_failed((uint8_t *)__FILE__, __LINE__)`，该函数用来指示出错的行数和文件。
&emsp;&emsp;对于`void assert_failed(uint8_t* file, uint32_t line);`，其英文注释说明了使用方法：

``` cpp
void assert_failed ( u8 *file, u32 line ) {
    /* User can add his own implementation to report the file name and linenumber,
       ex: printf("Wrong parameters value: file %s on line %d\r\n", file, line) */

    /* Infinite loop */
    while ( 1 ) { }
}
```