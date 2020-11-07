---
title: Security Manager Protocol
date: 2020-12-08 05:34:55
categories: BLE笔记
---
&emsp;&emsp;`SMP`使用固定的`L2CAP channel`(`CID`为`0x0006`)传输`Security`相关的`Command`。传统的配对方法使用默认的`L2CAP MTU`值`23`，`LE`安全连接配对方法将`L2CAP MTU`值扩大到`65`。<!--more-->
&emsp;&emsp;安全管理协议的操作以`Command`的形式进行，其格式如下：

&emsp;&emsp;`Code`代表了不同的命令类型，全部`Code`如下：

&emsp;&emsp;它主要从以下几个方面定义`SM`的行为：

&emsp;&emsp;1. 规定`L2CAP Channel`的特性，例如`MTU`、`QoS`等。
&emsp;&emsp;2. 规定`SM`命令格式。
&emsp;&emsp;3. 定义配对相关的命令，包括：

- `Pairing Request`
- `Pairing Response`
- `Pairing Confirm`
- `Pairing Random`
- `Pairing Failed`
- `Pairing Public Key`
- `Pairing DHKey Check`
- `Keypress Notification`

&emsp;&emsp;4. 定义鉴权、配对、密码交互等各个过程。

&emsp;&emsp;下面介绍`Pairing Request`的命令格式，`Pairing Response`与之完全相同。发起端设备发起`Pairing Request`，执行配对信息交换(`Pairing Feature Exchange`)。`Pairing Request`命令的结构如下：

- `IO Capability`：表示不同的`IO`能力。
- `OOB data flag`：表示是否具有`OOB`能力。
- `AuthReq`：表示认证请求，它的结构如下：

1. `Bonding_Flags`：是否保存绑定信息。
2. `MITM`：是否具有`MITM`防护能力，即是否需要认证。
3. `SC`(`Secure Connection`)：是否使用`LE Secure Connections Pairing`。
4. `Keypress`：为`KeyboardOnly`设备提供一些必要的输入状态信息。
5. `CT2`：与经典蓝牙有关。

- `Max Encryption Key Size`：表示最大密钥长度，有效范围为`7`至`16`字节。
- `Initiator Key Distribution`：表示发起者需要分发的密钥。
- `Responder Key Distribution`：表示响应者需要分发的密钥。