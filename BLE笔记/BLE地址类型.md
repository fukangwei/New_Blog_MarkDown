---
title: BLE地址类型
date: 2020-11-23 20:31:13
categories: BLE笔记
---
&emsp;&emsp;一个`BLE`设备，可以使用两种类型的地址(一个`BLE`设备可同时具备两种地址)：`Public Device Address`和`Random Device Address`。而`Random Device Address`又分为`Static Device Address`和`Private Device Address`两类。其中`Private Device Address`又可以分为`Non-Resolvable Private Address`和`Resolvable Private Address`。它们的关系如下图所示：<!--more-->

### Public Device Address

&emsp;&emsp;在通信系统中，设备地址是用来唯一地识别一个物理设备，例如`TCP/IP`协议中的`MAC`地址。对经典蓝牙(`BR/EDR`)来说，其设备地址是一个`48 bits`的数字，称作`48 bit universal LAN MAC addresses`。
&emsp;&emsp;这种地址分配方式在`BLE`中也保留下来了，就是`Public Device Address`，它是由`24 bit`的`Company_Id`和`24 bit`的`Company_Assigned`组成。

### Random Device Address

&emsp;&emsp;但是在`BLE`时代，只有`Public Device Address`还不够，有如下原因：

- `Public Device Address`需要向`IEEE`购买，相比于`BLE`的`IC`成本，还是一笔不小的开销。
- `Public Device Address`的申请与管理是相当繁琐、复杂的一件事情，再加上BLE设备的数量众多，导致维护成本增大。
- 安全因素。`BLE`很大一部分的应用场景是广播通信，这意味着只要知道设备的地址，就可以获取所有的信息，这是不安全的。因此，固定的设备地址加大了信息泄漏的风险。

&emsp;&emsp;为了解决上述问题，`BLE`协议新增了一种地址：`Random Device Address`，即设备地址不是固定分配的，而是在设备启动后随机生成的。根据不同的目的，`Random Device Address`分为`Static Device Address`和`Private Device Address`两类。

### Static Device Address

&emsp;&emsp;`Static Device Address`设备在上电时随机生成的地址：

- 最高两`bit`恒定为`11b`。
- 上电的时候随机生成，并且在一个上电周期内保持不变。
- 除了最高两位，剩余的`46 bits`是一个随机数，不能全部为`0`，也不能全部为`1`。
- 下一次上电的时候可以改变。但不是强制的，因此也可以保持不变。

### Private Device Address

&emsp;&emsp;`Static Device Address`通过地址随机生成的方式，解决了部分问题。`Private Device Address`通过定时更新和地址加密两种方法，进一步提高了蓝牙地址的可靠性和安全性。根据地址是否加密，`Private Device Address`又分为`Non-Resolvable Private Address`和`Resolvable Private Address`。
&emsp;&emsp;`Non-Resolvable Private Address`和`Static Device Address`类似，不同之处在于，`Non-Resolvable Private Address`会定时更新。更新的周期是由`GAP`规定的，称作`T_GAP`(`private_addr_int`)，建议值是`15`分钟。

&emsp;&emsp;`Resolvable Private Address`比较有用，它通过一个随机数和一个称作`Identity Resolving Key`(`IRK`)的密码生成，因此只能被拥有相同`IRK`的设备扫描到，可以防止被未知设备扫描和追踪。

1. 高位`24 bits`是随机数部分，其中最高位的两个`bit`为`10b`，用于标识地址类型；低位`24 bits`是随机数和`IRK`经过`hash`运算得到的`hash`值，运算的公式为`hash = ah(IRK, prand)`。
2. 当对端`BLE`设备扫描到该类型的蓝牙地址后，会使用保存在本机的`IRK`和该地址中的`prand`进行同样的`hash`运算，并将运算结果和地址中的`hash`字段比较。相同的时候，才进行后续的操作，这个过程被称作`Resolve`(解析)。
3. 以`T_GAP`(`private_addr_int`)为周期，定时进行更新。哪怕在广播、扫描、已连接等过程中，也可能改变设备地址。
4. `Resolvable Private Address`不能单独使用，因此需要使用该类型地址的话，设备要具备`Public Device Address`或`Static Device Address`中的一种。

### Resolvable Private Address应用场景以及HCI命令

&emsp;&emsp;`Resolvable Private Address`的解析和过滤操作是在`Link Layer`实现的，因此为`BLE`的广播通信提供了一个相对安全的加密环境。`Link Layer`以`Resolving List`的形式，通过`HCI`向`Host`提供相关的控制`API`，以实现相应的功能。相关的`HCI Command`如下：

- `LE Set Random Address Command`：设置一个新的`Random`地址，包括`Resolvable Private Address`类型的地址。
- `LE Add Device to Resolving List Command`：将指定的设备添加到本机的`Resolving List`中，需要指定的参数包括：需要添加设备的地址(包括地址类型)、需要添加设备的`IRK`以及本设备的`IRK`。
- `LE Remove Device From Resolving List Command`：将指定设备从本机的`Resolving List`中删除。
- `LE Clear Resolving List Command`：清除本机的`Resolving List`。
- `LE Read Resolving List Size Command`：读取本机`Resolving List`的大小。
- `LE Read Peer Resolvable Address Command`：读取对端设备解析后的`Resolvable Private Address`。
- `LE Read Local Resolvable Address Command`：读取本机设备解析后的`Resolvable Private Address`。
- `LE Set Address Resolution Enable Command`：`禁止/使能`地址解析功能。