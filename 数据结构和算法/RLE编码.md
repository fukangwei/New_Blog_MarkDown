---
title: RLE编码
categories: 数据结构和算法
abbrlink: ce4490f
date: 2019-02-09 12:22:54
---
&emsp;&emsp;游程编码(`RLE`，`run-length encoding`)，又称`行程长度编码`或`变动长度编码法`，是一种与资料性质无关的无损数据压缩技术。变动长度编码法为一种`使用变动长度的码来取代连续重复出现的原始资料`的压缩技术。
&emsp;&emsp;举例来说，一组资料串`AAAABBBCCDEEEE`由`4`个`A`、`3`个`B`、`2`个`C`、`1`个`D`、`4`个`E`组成，经过变动长度编码法可将资料压缩为`4A3B2C1D4E`(由`14`个单位转成`10`个单位)，其优点在于将重复性高的资料量压缩成小单位。其缺点在于：若该资料出现频率不高，可能导致压缩结果资料量比原始资料大，例如原始资料`ABCDE`的压缩结果为`1A1B1C1D1E`(由`5`个单位转成`10`个单位)。

### 整数固定长度表示法

#### 4位元表示法

&emsp;&emsp;顾名思义，利用`4`个位元来储存整数，以符号`C`表示整数值。其可表现的最大整数值为`15`，因此若资料重复出现次数超过`15`，便以`分段`方式处理。
&emsp;&emsp;假设某资料出现`N`次，则可以将其分成`(N / 15) + 1`段落来处理，其中`N / 15`的值为无条件舍去小数。例如连续出现`33`个`A`：

``` cpp
AAAAAAAAAAAAAAA AAAAAAAAAAAAAAA AAA
```

压缩结果为`15A15A3A`。内部储存码如下：

``` cpp
1111 01000001 1111 01000001 0011 01000001
 15      A     15      A      3      A
```

#### 8位元表示法

&emsp;&emsp;与`4`位元表示法的概念相同，其能表示最大整数为`255`。假设某资料出现`N`次，则可以将其分成`(N / 255) + 1`段落来处理，其中`N / 255`的值为无条件舍去小数。

### 压缩策略

#### 压缩

&emsp;&emsp;先使用一个暂存函数`Q`读取第一个资料，接着将下一个资料与`Q`值比。若资料相同，则计数器加`1`；若资料不同，则将计数器存的数值以及`Q`值输出，再初始计数器为`0`，`Q`值改为下一个资料。以此类推，完成资料压缩。

``` cpp
input: AAABCCBCCCCAA
​
for i = 1:size(input)
    if(Q = input(i))
        计数器 + 1
    else
        output的前项 = 计数器的值，output的下一项 = Q值
        换成input(i)，计数器值换成0
    end
end
```

#### 解压缩

&emsp;&emsp;其方法为逐一读取整数(以`C`表示)与资料(以`B`表示)，将`C`与`B`的二进制码分别转成十进制整数以及原始资料符号，最后输出共`C`次资料`B`，即完成一次资料解压缩。接着重复上述步骤，完成所有资料输出。

``` cpp
#include <iostream>
​
using namespace std;
​
void Print ( char sz[] ) {
    char *temp = sz;
    char one = temp[0];
    int nCount = 1;
    cout << one << " ";
​
    while ( *temp ) {
        temp++;
​
        if ( one == *temp ) {
            nCount++;
        } else {
            cout << nCount << " ";
            nCount = 1;
            one = *temp;
            cout << one << " ";
        }
    }
}
​
int main() {
    Print ( "aaaaaabbbbccc" );
    return 0;
}
```