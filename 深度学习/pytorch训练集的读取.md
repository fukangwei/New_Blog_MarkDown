---
title: pytorch训练集的读取
categories: 深度学习
abbrlink: 4cb476bb
date: 2019-01-15 09:27:12
---
&emsp;&emsp;`pytorch`读取训练集是非常便捷的，只需要使用到`2`个类，即`torch.utils.data.Dataset`和`torch.utils.data.DataLoader`。<!--more-->

### torchvision.datasets的使用

&emsp;&emsp;对于常用数据集，可以使用`torchvision.datasets`直接进行读取。`torchvision.dataset`是`torch.utils.data.Dataset`的实现，该包提供了以下数据集的读取：`MNIST`、`COCO(Captioning and Detection)`、`LSUN Classification`、`ImageFolder`、`Imagenet-12`、`CIFAR10 and CIFAR100`以及`STL10`。

``` python
import torchvision
​
cifarSet = torchvision.datasets.CIFAR10(root="./cifar/", train=True, download=True)
print(cifarSet[0])
img, label = cifarSet[0]
print(img)
print(label)
print(img.format, img.size, img.mode)
img.show()
```

执行结果：

``` python
(<PIL.Image.Image image mode=RGB size=32x32 at 0x1726952ADA0>, 6)
<PIL.Image.Image image mode=RGB size=32x32 at 0x1726952ACF8>
6
None (32, 32) RGB
```

### 自定义数据集基础方法

&emsp;&emsp;首先要创建一个`Dataset`类：

``` python
from torch.utils.data.dataset import Dataset
​
class MyCustomDataset(Dataset):
    def __init__(self, ...):
        # stuff
​
    def __getitem__(self, index):
        # stuff
        return (img, label)
​
    def __len__(self):
        return count
```

在这个代码中，`__init__()`用于一些初始化过程，`__len__()`返回所有数据的数量，`__getitem__()`返回数据和标签，可以这样显式调用：

``` python
img, label = MyCustomDataset.__getitem__(99)
```

&emsp;&emsp;`Transform`最常见的使用方法是：

``` python
from torch.utils.data.dataset import Dataset
from torchvision import transforms
​
class MyCustomDataset(Dataset):
    def __init__(self, ..., transforms=None):
        # stuff
        ...
        self.transforms = transforms
​
    def __getitem__(self, index):
        # stuff
        ...
        data = ...  # 一些读取的数据
        # 如果transform不为None，则进行transform操作
        if self.transforms is not None:
            data = self.transforms(data)

        return (img, label)
​
    def __len__(self):
        return count
​
if __name__ == '__main__':
    # 定义我们的transforms(1)
    transformations = transforms.Compose([transforms.CenterCrop(100),
                                          transforms.ToTensor()])
    custom_dataset = MyCustomDataset(..., transformations)  # 创建dataset
```

&emsp;&emsp;有些人不喜欢把`transform`操作写在`Dataset`外面(上面代码里的注释`1`)，所以还有一种写法：

``` python
from torch.utils.data.dataset import Dataset
from torchvision import transforms
​
class MyCustomDataset(Dataset):
    def __init__(self, ...):
        # stuff
        ...
        # (2) 一种方法是单独定义transform
        self.center_crop = transforms.CenterCrop(100)
        self.to_tensor = transforms.ToTensor()
        # (3) 或者写成下面这样
        self.transformations = transforms.Compose([transforms.CenterCrop(100),
                                                   transforms.ToTensor()])
​
    def __getitem__(self, index):
        # stuff
        ...
        data = ...  # 一些读取的数据
        # 当第二次调用transform时，调用的是“__call__()”
        data = self.center_crop(data)  # (2)
        data = self.to_tensor(data)  # (2)
        # 或者写成下面这样
        data = self.trasnformations(data)  # (3)
        # 注意(2)和(3)中只需要实现一种
        return (img, label)
​
    def __len__(self):
        return count
​
if __name__ == '__main__':
    custom_dataset = MyCustomDataset(...)
```

&emsp;&emsp;假如说我们想从一个`csv`文件中读取数据，一个`csv`示例如下：

File Name  | Label | Extra Operation
-----------|-------|----------------
`tr_0.png` | `5`   | `TRUE`
`tr_1.png` | `0`   | `FALSE`
`tr_1.png` | `4`   | `FALSE`

如果我们需要在自定义数据集里从这个`csv`文件读取文件名，可以这样做：

``` python
class CustomDatasetFromImages(Dataset):
    def __init__(self, csv_path):
        """ 参数csv_path是csv文件路径 """
        self.to_tensor = transforms.ToTensor()  # Transforms
        self.data_info = pd.read_csv(csv_path, header=None)  # 读取csv文件
        self.image_arr = np.asarray(self.data_info.iloc[:, 0])  # 文件第一列包含图像文件的名称
        self.label_arr = np.asarray(self.data_info.iloc[:, 1])  # 第二列是图像的label
        self.operation_arr = np.asarray(self.data_info.iloc[:, 2])  # 第三列是决定是否进行额外操作
        self.data_len = len(self.data_info.index)  # 计算length
​
    def __getitem__(self, index):
        single_image_name = self.image_arr[index]  # 得到文件名
        img_as_img = Image.open(single_image_name)  # 读取图像文件
        some_operation = self.operation_arr[index]  # 检查需不需要额外操作

        if some_operation:  # 如果需要额外操作
            # ...
            pass

        img_as_tensor = self.to_tensor(img_as_img)  # 把图像转换成tensor
        single_image_label = self.label_arr[index]  # 得到图像的label
        return (img_as_tensor, single_image_label)
​
    def __len__(self):
        return self.data_len
​
if __name__ == "__main__":
    custom_mnist_from_images = CustomDatasetFromImages('../data/mnist_labels.csv')
```

&emsp;&emsp;另一种情况是`csv`文件中保存了我们需要的图像文件的像素值，这里需要改动一下`__getitem__()`函数：

Label | pixel_1 | pixel_2 | ...
------|---------|---------|-----
`1`   | `50`    | `99`    | `...`
`0`   | `21`    | `223`   | `...`
`9`   | `44`    | `112`   | `...`

代码如下：

``` python
class CustomDatasetFromCSV(Dataset):
    def __init__(self, csv_path, height, width, transforms=None):
        """
        参数csv_path是csv文件路径，height是图像高度，width是图像宽度，transform是transform操作
        """
        self.data = pd.read_csv(csv_path)
        self.labels = np.asarray(self.data.iloc[:, 0])
        self.height = height
        self.width = width
        self.transforms = transform
​
    def __getitem__(self, index):
        single_image_label = self.labels[index]
        # 读取所有像素值，并将“1D array ([784])”reshape成为“2D array ([28,28])”
        img_as_np = np.asarray(self.data.iloc[index][1:]).reshape(28, 28).astype('uint8')
        # 把“numpy array”格式的图像转换成灰度“PIL image”
        img_as_img = Image.fromarray(img_as_np)
        img_as_img = img_as_img.convert('L')

        if self.transforms is not None:
            img_as_tensor = self.transforms(img_as_img)  # 将图像转换成tensor

        return (img_as_tensor, single_image_label)  # 返回图像及其label
​
    def __len__(self):
        return len(self.data.index)
​
if __name__ == "__main__":
    transformations = transforms.Compose([transforms.ToTensor()])
    custom_mnist_from_csv = CustomDatasetFromCSV('./data/mnist_in_csv.csv', 28, 28, transformations)
```

&emsp;&emsp;`PyTorch`中的`Dataloader`只是调用`__getitem__()`方法并组合成`batch`，我们可以这样调用：

``` python
if __name__ == "__main__":
    transformations = transforms.Compose([transforms.ToTensor()])  # 定义transforms
    # 自定义数据集
    custom_mnist_from_csv = CustomDatasetFromCSV('./data/mnist_in_csv.csv',
                                                 28, 28, transformations)
    # 定义“data loader”
    mn_dataset_loader = torch.utils.data.DataLoader(dataset=custom_mnist_from_csv,
                                                    batch_size=10, shuffle=False)
​
    for images, labels in mn_dataset_loader:
        # 将数据传给网络模型
```

需要注意的是使用多`GPU`训练时，`PyTorch`的`dataloader`会将每个`batch`平均分配到各个`GPU`。所以如果`batch size`过小，可能发挥不了多`GPU`的效果。