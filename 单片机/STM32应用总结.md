---
title: STM32应用总结
date: 2018-12-30 04:29:19
categories: 单片机
---
### STM32软件延时函数

&emsp;&emsp;`stm32`系统时钟为`72MHz`，用软件延时方法实现微秒级延时：

``` c
#define delayUs(x) { unsigned int _dcnt; \
        _dcnt = (x * 16); \
        while(_dcnt-- > 0) \
        { continue; }\
    }
```

其中`x`为延时时间(单位为`us`)。在实际编程中，我认为`x * 16`应该改为`x * 5`，并且`_dcnt`用`static`关键词修饰。

### STM32关闭JTAG

&emsp;&emsp;在关闭`JTAG`的同时，记得要开启复用功能时钟和`IO`口时钟：

``` c
RCC->APB2ENR |= 0x00000001; /* 开启afio时钟 */
AFIO->MAPR = ( 0x00FFFFFF & AFIO->MAPR ) | 0x04000000; /* 关闭JTAG */
// GPIO_PinRemapConfig ( GPIO_Remap_SWJ_Disable, ENABLE ); /* 关闭JTAG */
```

### STM32的BOOT0和BOOT1

&emsp;&emsp;STM32三种启动模式对应的存储介质均是芯片内置的：

- `用户闪存`：芯片内置的`Flash`。
- `SRAM`：芯片内置的`RAM`区，就是内存。
- `系统存储器`：芯片内部一块特定的区域，芯片出厂时在这个区域预置了一段`Bootloader`，就是通常说的`ISP`程序。这个区域的内容在芯片出厂后没有人能够修改或擦除，即它是一个`ROM`区。

&emsp;&emsp;在每个`STM32`的芯片上都有两个管脚`BOOT0`和`BOOT1`，这两个管脚在芯片复位时的电平状态决定了芯片复位后从哪个区域开始执行程序：

- `BOOT1 = x`，`BOOT0 = 0`：从用户闪存启动，这是正常的工作模式。
- `BOOT1 = 0`，`BOOT0 = 1`：从系统存储器启动，这种模式启动的程序功能由厂家设置。
- `BOOT1 = 1`，`BOOT0 = 1`：从内置`SRAM`启动，这种模式可以用于调试。

&emsp;&emsp;要注意的是，一般不使用内置`SRAM`启动，因为`SRAM`掉电后数据就丢失。多数情况下`SRAM`只是在调试时使用，也可以做其他一些用途，例如做故障的局部诊断，写一段小程序加载到`SRAM`中诊断板上的其他电路，或用此方法读写板上的`Flash`或`EEPROM`等。还可以通过这种方法解除内部`Flash`的读写保护，当然解除读写保护的同时`Flash`的内容也被自动清除，以防止恶意的软件拷贝。
&emsp;&emsp;通常`BOOT0`和`BOOT1`通过跳线接到`GND`，只是在`ISP`下载的情况下，`BOOT0 = 1`且`BOOT1 = 0`。下载完成后，把`BOOT0`的跳线接回`0`，也就是`BOOT0 = 0`且`BOOT1 = 0`。
&emsp;&emsp;`STM32`的硬件设计指导也是建议`BOOT0`和`BOOT1`接电阻再接地的。`BOOT0`和`BOOT1`分别通过`10k`电阻接地，方便串口下载。下载器控制`BOOT0`为高电平，下载完后又将`BOOT0`恢复为低，`STM32`复位即可进入用户程序。`BOOT0`接`10k`电阻接地或者直接接地，都不会引起芯片损毁。

### Keil for ARM使用malloc函数

&emsp;&emsp;需要使用微库，否则会报错，例如`.\Output\SaiWu.axf: Error: L6915E: Library reports error: __use_no_semihosting was requested, but _ttywrch was referenced`。
&emsp;&emsp;在使用`malloc`的`C`文件中包含`stdlib.h`，到这里就可以使用`malloc`函数了，但是不能分配较大的内存，这是因为`Keil`默认给`heap`分配了很小的内存。`STM32F103`单片机中的启动文件为`heap`分配了`512`字节的内存(可能不同的单片机默认分配的不同)，将其(`Heap_Size`)修改为`10K`大小。