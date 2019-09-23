---
title: 可视化CNN中间层
date: 2019-01-01 11:23:20
categories: 深度学习
---
&emsp;&emsp;主要的实现思路如下：

&emsp;&emsp;1. 处理单张图片作为网络输入。
&emsp;&emsp;2. 根据给定的`layer`层，获取该层的输出结果`features`。
&emsp;&emsp;3. 考虑到`features`的形状为`[batch_size, filter_nums, H, W]`，提取其中的第一个过滤器得到的结果`feature`。
&emsp;&emsp;4. 以一张图片作为输入的情况下，我们得到的`feature`即为`[H, W]`大小的`tensor`。
&emsp;&emsp;5. 将`tensor`转为`numpy`，然后归一化到`[0, 1]`，最后乘以`255`，使得范围为`[0, 255]`。
&emsp;&emsp;6. 得到灰度图像并保存。

``` python
import cv2
import numpy as np
import torch
from torch.autograd import Variable
from torchvision import models
​
def preprocess_image(cv2im, resize_im=True):
    """
    function: Processes image for CNNs.
    Args: PIL_img (PIL_img): Image to process; resize_im (bool): Resize to 224 or not.
    returns: im_as_var (Pytorch variable): Variable that contains processed float tensor
    """
    # mean and std list for channels (Imagenet)
    mean = [0.485, 0.456, 0.406]
    std = [0.229, 0.224, 0.225]

    if resize_im:  # Resize image
        cv2im = cv2.resize(cv2im, (224, 224))

    im_as_arr = np.float32(cv2im)
    im_as_arr = np.ascontiguousarray(im_as_arr[..., ::-1])
    im_as_arr = im_as_arr.transpose(2, 0, 1)  # Convert array to D,W,H
​
    for channel, _ in enumerate(im_as_arr):  # Normalize the channels
        im_as_arr[channel] /= 255
        im_as_arr[channel] -= mean[channel]
        im_as_arr[channel] /= std[channel]
​
    im_as_ten = torch.from_numpy(im_as_arr).float()  # Convert to float tensor
    im_as_ten.unsqueeze_(0)  # Add one more channel to the beginning. Tensor shape = 1,3,224,224
    im_as_var = Variable(im_as_ten, requires_grad=True)  # Convert to Pytorch variable
    return im_as_var
​
class FeatureVisualization():
    def __init__(self, img_path, selected_layer):
        self.img_path = img_path
        self.selected_layer = selected_layer
        self.pretrained_model = models.vgg16(pretrained=True).features
​
    def process_image(self):
        img = cv2.imread(self.img_path)
        img = preprocess_image(img)
        return img
​
    def get_feature(self):
        input = self.process_image()
        print("get_feature:", input.shape)

        x = input

        for index, layer in enumerate(self.pretrained_model):
            x = layer(x)
            if (index == self.selected_layer):
                return x
​
    def get_single_feature(self):
        features = self.get_feature()
        print("get_single_feature_1:", features.shape)
        feature = features[:, 0, :, :]
        print("get_single_feature_2:", feature.shape)
        feature = feature.view(feature.shape[1], feature.shape[2])
        print("get_single_feature_3:", feature.shape)
        return feature
​
    def save_feature_to_img(self):
        feature = self.get_single_feature()
        feature = feature.data.numpy()
        feature = 1.0 / (1 + np.exp(-1 * feature))  # use sigmod to [0, 1]
        feature = np.round(feature * 255)  # to [0, 255]
        cv2.imwrite('./img.jpg', feature)
​
if __name__ == '__main__':
    myClass = FeatureVisualization('./tu.jpg', 5)
    print(myClass.pretrained_model)
    myClass.save_feature_to_img()
```