---
title: tqdm模块
categories: Python语法
date: 2018-12-27 17:45:22
---
&emsp;&emsp;`tqdm`是一个进度条，可以在`Python`循环中添加一个进度提示信息。<!--more-->

### 基本用法

&emsp;&emsp;基本用法如下：

``` python
from tqdm import tqdm
from time import sleep

# 进度条的长度是9
for i in tqdm(range(9)):
    sleep(0.1)
```

执行结果：

``` bash
100%|██████████| 9/9 [00:00<00:00,  9.94it/s]
```

### 添加描述

&emsp;&emsp;还可以为进度条添加描述：

``` python
from tqdm import tqdm
from time import sleep

pbar = tqdm(["a", "b", "c", "d"])

for char in pbar:
    pbar.set_description("Processing %s" % char)
    sleep(1)
```

执行结果：

``` bash
Processing c:  50%|█████     | 2/4 [00:02<00:02,  1.00s/it]
```

### 手动控制更新

&emsp;&emsp;除了自动的更新方式，还可以手动地控制更新：

``` python
from tqdm import tqdm
from time import sleep

with tqdm(total=100) as pbar:
    for i in range(10):
        sleep(1)
        pbar.update(10)
```

执行结果：

``` bash
40%|████      | 40/100 [00:04<00:06, 10.00it/s]
```