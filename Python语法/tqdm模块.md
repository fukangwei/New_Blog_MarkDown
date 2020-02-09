---
title: tqdm模块
categories: Python语法
abbrlink: 3500ab35
date: 2018-12-27 17:45:22
---
&emsp;&emsp;`Tqdm`是一个快速的、可扩展的`Python`进度条，可以在`Python`长循环中添加一个进度提示信息，用户只需要封装迭代器`tqdm(iterator)`即可。<!--more-->
&emsp;&emsp;最基本的用法如下，该进度条的长度是`9`：

``` python
from tqdm import tqdm
from time import sleep
​
for i in tqdm(range(9)):
    sleep(0.1)
```

执行结果：

``` bash
100%|██████████| 9/9 [00:00<00:00,  9.94it/s]
```

还可以为进度条添加描述：

``` python
from tqdm import tqdm
from time import sleep
​
pbar = tqdm(["a", "b", "c", "d"])
​
for char in pbar:
    pbar.set_description("Processing %s" % char)
    sleep(1)
```

执行结果：

``` bash
Processing c:  50%|█████     | 2/4 [00:02<00:02,  1.00s/it]
```

&emsp;&emsp;除了自动的更新方式，还可以手动地控制更新：

``` python
from tqdm import tqdm
from time import sleep
​
with tqdm(total=100) as pbar:
    for i in range(10):
        sleep(1)
        pbar.update(10)
```

执行结果：

``` bash
40%|████      | 40/100 [00:04<00:06, 10.00it/s]
```