---
title: Python环境搭建
categories: 软件与硬件问题
date: 2019-01-13 19:00:42
---
### pip安装和入门

&emsp;&emsp;对于`linux`系统，可以使用如下命令进行安装：<!--more-->

``` bash
sudo apt-get install python3-pip
```

`pip`基本用法如下：

``` bash
pip install Package  # 安装包
pip list --outdated  # 检查哪些包需要更新
pip install Package -U # 升级包
pip uninstall Package  # 卸载包
pip -V  # 查看当前pip对应的python版本(“python -V”查看python版本)
pip freeze > requirements.txt  # 生成requirements.txt文件
pip install -r requirements.txt  # 安装requirements.txt依赖
```

### 打开ipynb文件

&emsp;&emsp;首先需要安装`ipython`和`jupyter`，使用如下命令(`Windows`系统需要去掉`sudo`)：

``` bash
sudo pip install ipython --upgrade
sudo pip install jupyter
```

接下来需要在终端中输入：

``` bash
jupyter notebook
```

即可在浏览器中打开当前目录。可以通过切换目录来选择需要打开的`ipynb`文件。

---

### TensorFlow的安装

#### Windows版本

&emsp;&emsp;如果只是安装`cpu`版本，则使用如下命令：

``` bash
pip install tensorflow
```

如果需要安装`gpu`版本，首先确保有`NVIDIA`的显卡并安装好驱动，然后还要安装`CUDA`和`cuDNN`库。
&emsp;&emsp;首先去`TensorFlow`的官网查看支持的`CUDA`版本，然后去`https://developer.nvidia.com/cuda-downloads`下载对应的`CUDA`，并进行安装。
&emsp;&emsp;再去`https://developer.nvidia.com/rdp/cudnn-download#a-collapse705-91`下载`cuDNN`库，名称类似于`cudnn-9.1-windows10-x64-v7.1.zip`。下载完成并解压，里面有`3`个文件夹，需要复制到`C:\Program Files\NVIDIA GPU Computing Toolkit\CUDA\v9.1`文件夹下覆盖。
&emsp;&emsp;最后安装`TensorFlow`的`gpu`版本(当时是`1.9.0`版本)，使用如下命令：

``` bash
pip install tensorflow-gpu==1.9.0
```

&emsp;&emsp;进入`Python`，对安装的`TensorFlow`进行测试：

``` python
import tensorflow as tf

hello = tf.constant('hello, TensorFlow!')
sess = tf.Session()
print(sess.run(hello))
```

如果输出了`GPU`的信息，说明安装成功。

#### Linux版本

&emsp;&emsp;首先查看所安装的`CUDA`版本：

``` python
cat /usr/local/cuda/version.txt
```

然后查看安装的`cuDNN`版本：

``` python
cat /usr/local/cuda/include/cudnn.h | grep CUDNN_MAJOR -A 2
```

假设`CUDA`版本是`8.0`，`cuDNN`版本是`6.0`，可以安装`TensorFlow`的`1.4.0`版本：

``` bash
pip install tensorflow==1.4.0
```

---

### 远程访问“Jupyter Notebook”

&emsp;&emsp;在远程服务器上启动`jupyter notebooks`服务：

``` bash
jupyter notebook --no-browser --port=8889
```

在本地终端中启动`ssh`：

``` bash
ssh -N -f -L localhost:8888:localhost:8889 username@serverIP
```

其中`-N`告诉`ssh`没有命令要被远程执行，`-f`告诉`ssh`在后台执行，`-L`指定`port forwarding`的配置，远端端口是`8889`，本地的端口号的`8888`。注意`username@serverIP`替换成服务器的对应账号。最后打开浏览器，访问`http://localhost:8888/`。

---

### 使用conda创建python虚拟环境

&emsp;&emsp;首先在系统中安装`Anaconda`，可以输入`conda -V`查看是否安装成功以及当前`conda`的版本。`conda`常用命令如下：

``` bash
conda list  # 查看安装了的包
conda env list  # 查看当前存在的虚拟环境
conda update conda  # 检查更新当前的conda
```

&emsp;&emsp;使用命令`conda create -n your_env_name python=X.X`创建`python`版本为`X.X`，名字为`your_env_name`的虚拟环境，随后可以在`Anaconda`中的`envs`目录下找到`your_env_name`文件，这里的`X.X`可以是`2.7`或`3.6`等。
&emsp;&emsp;使用激活(或切换不同`python`版本)的虚拟环境：

``` bash
source activate your_env_name  # Linux系统
activate your_env_name  # Windows系统
```

&emsp;&emsp;使用命令`conda install -n your_env_name package`即可安装`package`到`your_env_name`中。或者进入`conda`虚拟环境，使用`conda install package`(或者`conda install package=版本`)安装`package`。
&emsp;&emsp;关闭虚拟环境(即从当前虚拟环境中退出，使用`PATH`环境中的默认`python`)：

``` bash
source deactivate  # Linux系统
deactivate  # Windows系统
```

&emsp;&emsp;使用命令`conda remove -n your_env_name --all`即可删除指定的虚拟环境。
&emsp;&emsp;使用命令`conda remove -n your_env_name package_name`删除虚拟环境中的`package_name`包。或者进入`conda`虚拟环境，使用`conda uninstall package`卸载`package`。