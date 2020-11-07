---
title: 安全机制之LE Encryption
date: 2020-12-06 07:32:46
categories: BLE笔记
---
### LE加密/解密过程

&emsp;&emsp;`LE`加密/解密的过程如下图所示：<!--more-->

&emsp;&emsp;`Master/Slave`的`LE Host`会保存一个`LTK`(`Long Term Key`，至少`128 bits`)，对`BLE`用户(或者应用)来说，这个`Key`是所有加密/解密动作源头。
&emsp;&emsp;每当为某个`LE`连接启动加密传输时，`Master`和`Slave`的`LL`会协商生成一个`128 bits`的随机数`SKD`(`Session Key Diversifier`，`128 bits`)，并以它为输入，以`LTK`为`Key`，通过`Encryption Engine`加密生成`SessionKey`。
&emsp;&emsp;每当有明文数据包需要发送时，要对明文进行加密。加密的过程是以明文数据包为输入，以`SessionKey`为`Key`，同样通过`Encryption Engine`加密生成密文数据包。
&emsp;&emsp;同样，每当收到密文数据包时，要对密文解密。解密的过程是以密文数据包为输入，以`SessionKey`为`Key`，同样通过`Encryption Engine`解密生成明文数据包。

### Encryption Procedure

&emsp;&emsp;`LE Encryption`的过程主要由`Link Layer`控制。当连接建立之后，`Link Layer`可以应`Host`的请求，使能对数据包的`Encryption`操作：

&emsp;&emsp;`Host A`发送`LE Start Encryption Command`，请求`Link Layer`启动加密。该`Command`的格式如下：

- `Connection_Handle`：连接句柄。
- `Random_Number`和`Encrypted_Diversifier`：分别简称为`Rand`和`EDIV`(`Rand`是一个`64 bits`的随机数，`EDIV`是一个`16 bits`的`Diversifier`)，它们在`LE Legacy Pairing`的过程中，用于在多个`LTK`中标识某一个具体的`LTK`。而在新的`LE Secure Connections Pairing`过程中，则不再使用(赋值为`0`即可)。
- `Long_Term_Key`：保存在`Host`段中的加密`key`。

&emsp;&emsp;`LL A`收到`Host`的加密请求之后，会向`LL B`发送`LL_ENC_REQ PDU`以请求加密，该`PDU`的格式如下：

- `Rand`和`EDIV`：就是上面的`Random_Number`和`Encrypted_Diversifier`。
- `SKDm`(`Session Key Diversifier`)：一个`64 bits`的随机数，用于和`SKDs`一起生成本次加密的`SessionKey`。
- `IVm`(`Initialization Vector`)：一个`32 bits`的随机数，和`IVs`一起组成`64 bits`的`IV`，后面`Encryption Engine`会使用。

&emsp;&emsp;`LL B`收到`LL_ENC_REQ PDU`之后，会向`Host B`发送`LE Long Term Key Request Event`，该`Event`的格式如下：

- `Subevent_Code`：其值为`0x05`。
- `Connection_Handle`：连接句柄。
- `Random_Number`和`Encrypted_Diversifier`：就是`LL_ENC_REQ PDU`中的`Rand`和`EDIV`，最初是由`Host A`通过`LE Start Encryption`命令发送过来的。

&emsp;&emsp;如果`Host B`能提供`LTK`，则通过`LE Long Term Key Request Reply`命令，将`LTK`提供给`LL B`，该命令的格式如下：

&emsp;&emsp;`LL B`收到`LTK`之后，会向`LL A`回应一个`LL_ENC_ RSP PDU`：

&emsp;&emsp;`SKDs`和`LL A`的`SDKm`共同组成`128 bits`的`SKD`；`IVs`和`LL A`的`IVm`共同组成`64 bits`的`IV`。
&emsp;&emsp;`LL A`收到`LL_ENC_RSP PDU`后，可以向`LL B`发送`LL_START_ENC_REQ PDU`，开启`Encryption`；`LL B`则回应`LL_START_ENC_RSP PDU`。这两个`PDU`均不携带任何的参数。
&emsp;&emsp;加密`Start`之后，双方就可以安全地通信了。当然，`LE encryption`还提供了其它诸如暂停加密(`LL_PAUSE_ENC_REQ/LL_PAUSE_ENC_RSP`)、重启加密等过程。