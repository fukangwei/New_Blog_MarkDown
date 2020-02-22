---
title: Pytorch模型可视化
categories: 深度学习
date: 2019-01-09 17:04:20
---
&emsp;&emsp;`torchsummary`可以用于模型的可视化，它会输出模型各层的详细参数以及输出尺寸。其安装方法如下：<!--more-->

``` bash
pip install torchsummary
```

使用`torchsummary`直接调用`summary`即可，参数分别为`model`和输入`tensor`的尺寸：

``` python
from torchsummary import summary
summary(your_model, input_size=(channels, H, W))
```

简单的例子如下：

``` python
import torch
from torchsummary import summary
from torchvision.models import vgg11

model = vgg11(pretrained=False)

if torch.cuda.is_available():
    model = model.cuda()

summary(model, (3, 224, 224))
```

执行结果：

``` python
----------------------------------------------------------------
        Layer (type)               Output Shape         Param #
================================================================
            Conv2d-1         [-1, 64, 224, 224]           1,792
              ReLU-2         [-1, 64, 224, 224]               0
         MaxPool2d-3         [-1, 64, 112, 112]               0
            Conv2d-4        [-1, 128, 112, 112]          73,856
              ReLU-5        [-1, 128, 112, 112]               0
         MaxPool2d-6          [-1, 128, 56, 56]               0
            Conv2d-7          [-1, 256, 56, 56]         295,168
              ReLU-8          [-1, 256, 56, 56]               0
            Conv2d-9          [-1, 256, 56, 56]         590,080
             ReLU-10          [-1, 256, 56, 56]               0
        MaxPool2d-11          [-1, 256, 28, 28]               0
           Conv2d-12          [-1, 512, 28, 28]       1,180,160
             ReLU-13          [-1, 512, 28, 28]               0
           Conv2d-14          [-1, 512, 28, 28]       2,359,808
             ReLU-15          [-1, 512, 28, 28]               0
        MaxPool2d-16          [-1, 512, 14, 14]               0
           Conv2d-17          [-1, 512, 14, 14]       2,359,808
             ReLU-18          [-1, 512, 14, 14]               0
           Conv2d-19          [-1, 512, 14, 14]       2,359,808
             ReLU-20          [-1, 512, 14, 14]               0
        MaxPool2d-21            [-1, 512, 7, 7]               0
           Linear-22                 [-1, 4096]     102,764,544
             ReLU-23                 [-1, 4096]               0
          Dropout-24                 [-1, 4096]               0
           Linear-25                 [-1, 4096]      16,781,312
             ReLU-26                 [-1, 4096]               0
          Dropout-27                 [-1, 4096]               0
           Linear-28                 [-1, 1000]       4,097,000
================================================================
Total params: 132,863,336
Trainable params: 132,863,336
Non-trainable params: 0
----------------------------------------------------------------
Input size (MB): 0.57
Forward/backward pass size (MB): 125.18
Params size (MB): 506.83
Estimated Total Size (MB): 632.59
```

&emsp;&emsp;代码实例二如下：

``` python
import torch
import torch.nn as nn
import torch.nn.functional as F
from torchsummary import summary

class Net(nn.Module):
    def __init__(self):
        super(Net, self).__init__()
        self.conv1 = nn.Conv2d(1, 10, kernel_size=5)
        self.conv2 = nn.Conv2d(10, 20, kernel_size=5)
        self.conv2_drop = nn.Dropout2d()
        self.fc1 = nn.Linear(320, 50)
        self.fc2 = nn.Linear(50, 10)

    def forward(self, x):
        x = F.relu(F.max_pool2d(self.conv1(x), 2))
        x = F.relu(F.max_pool2d(self.conv2_drop(self.conv2(x)), 2))
        x = x.view(-1, 320)
        x = F.relu(self.fc1(x))
        x = F.dropout(x, training=self.training)
        x = self.fc2(x)
        return F.log_softmax(x, dim=1)

model = Net()

if torch.cuda.is_available():
    model = model.cuda()

summary(model, (1, 28, 28))
```

执行结果：

``` python
----------------------------------------------------------------
        Layer (type)               Output Shape         Param #
================================================================
            Conv2d-1           [-1, 10, 24, 24]             260
            Conv2d-2             [-1, 20, 8, 8]           5,020
         Dropout2d-3             [-1, 20, 8, 8]               0
            Linear-4                   [-1, 50]          16,050
            Linear-5                   [-1, 10]             510
================================================================
Total params: 21,840
Trainable params: 21,840
Non-trainable params: 0
----------------------------------------------------------------
Input size (MB): 0.00
Forward/backward pass size (MB): 0.06
Params size (MB): 0.08
Estimated Total Size (MB): 0.15
```

&emsp;&emsp;多输入代码如下：

``` python
import torch
import torch.nn as nn
from torchsummary import summary

class SimpleConv(nn.Module):
    def __init__(self):
        super(SimpleConv, self).__init__()
        self.features = nn.Sequential(
            nn.Conv2d(1, 1, kernel_size=3, stride=1, padding=1),
            nn.ReLU(),
        )

    def forward(self, x, y):
        x1 = self.features(x)
        x2 = self.features(y)
        return x1, x2

device = torch.device("cuda" if torch.cuda.is_available() else "cpu")
model = SimpleConv().to(device)
summary(model, [(1, 16, 16), (1, 28, 28)])
```

执行结果：

``` python
----------------------------------------------------------------
        Layer (type)              Output Shape          Param #
================================================================
            Conv2d-1            [-1, 1, 16, 16]              10
              ReLU-2            [-1, 1, 16, 16]               0
            Conv2d-3            [-1, 1, 28, 28]              10
              ReLU-4            [-1, 1, 28, 28]               0
================================================================
Total params: 20
Trainable params: 20
Non-trainable params: 0
----------------------------------------------------------------
Input size (MB): 0.77
Forward/backward pass size (MB): 0.02
Params size (MB): 0.00
Estimated Total Size (MB): 0.78
```