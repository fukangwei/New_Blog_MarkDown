---
title: Python问题总结
date: 2019-01-10 16:01:38
tags:
---
### HTTP Error 403: Forbidden

&emsp;&emsp;`urllib.request.urlopen`经常会被用来打开一个网页的源代码，然后会去分析这个页面源代码。但是对于有的网站使用这种方法时，会抛出`HTTP Error 403: Forbidden`异常。例如执行下面的语句：

``` python
urllib.request.urlopen("http://blog.csdn.net/eric_sunah/article/details/11099295")
```

会出现以下异常：

``` python
"D:\Python\lib\urllib\request.py", in open response = meth(req, response)
"D:\Python\lib\urllib\request.py", in http_response 'http', request, response, code, msg, hdrs)
"D:\Python\lib\urllib\request.py", in error return self._call_chain(*args)
"D:\Python\lib\urllib\request.py", in _call_chain result = func(*args)
"D:\Python\lib\urllib\request.py", in http_error_default raise HTTPError(req.full_url, code, msg, hdrs, fp)
urllib.error.HTTPError: HTTP Error 403: Forbidden
```

&emsp;&emsp;之所以出现上面的异常，是因为如果用`urllib.request.urlopen`方式打开一个`URL`，服务器端只会收到一个单纯的对于该页面访问的请求，但是服务器并不知道发送这个请求使用的浏览器、操作系统、硬件平台等信息，而缺失这些信息的请求往往都是非正常的访问，例如爬虫。有些网站为了防止这种非正常的访问，会验证请求信息中的`UserAgent`(它的信息包括硬件平台、系统软件、应用软件和用户个人偏好)。如果`UserAgent`存在异常或者是不存在，那么这次请求将会被拒绝(例如上面的错误信息所示)，所以可以尝试在请求中加入`UserAgent`的信息。
&emsp;&emsp;对于`Python 3`来说，在请求中添加`UserAgent`的信息非常简单：

``` python
# 如果不加上下面的这行，出现会出现“urllib2.HTTPError: HTTP Error 403: Forbidden”错误
# 主要是由于该网站禁止爬虫导致的，可以在请求加上头信息，伪装成浏览器访问“User-Agent”，
# 具体的信息可以通过火狐的FireBug插件查询
headers = {'User-Agent': 'Mozilla/5.0 (Windows NT 6.1; WOW64; rv:23.0) Gecko/20100101 Firefox/23.0'}
req = urllib.request.Request(url=chaper_url, headers=headers)
urllib.request.urlopen(req).read()
```

将`urllib.request.urlopen.read`替换成上面的代码后，对于出现问题的页面就可以就正常访问。如果加上这个`UserAgent`还是不行的话，那就换一个`UserAgent`吧。

### serial.serialutil.SerialException: could not open port

&emsp;&emsp;`Question`：I tried to communicate `Python` and `Arduino` with `pyserial` on Win8, but it had error like this：

``` python
Traceback (most recent call last):
File "C:\Users\Fon\Desktop\x.py", line 7, in <module> ser.open() # open serial port
File "C:\Python27\lib\site-packages\serial\serialwin32.py", line 66, \
in open raise SerialException("could not open port %r: %r" % (self.portstr, ctypes.WinError())) \
serial.serialutil.SerialException: could not open port 'COM4': \
WindowsError(2, 'The system cannot find the file specified.')
```

this is my code：

``` python
import serial
​
ser = serial.Serial()
ser.port = 3  # serial port
ser.baudrate = 115200  # set baudrate 115200
ser.timeout = 60  # timeout 60 second
ser.open()  # open serial port
​
while True:
    ser.write('l')  # send '1' to port to get light
    light = ser.read(4)
    print "light", light
```

in this code，I tried to open port `COM4`，I already check this port is available and I already tried another port but it not worked any port. Am I using wrong port？or something？
&emsp;&emsp;`Answer`：Does the port even exist? Maybe there is something already connected to it. If you ran the program before and the serial port didn't close correctly that old program may still be connected to it. If it doesn't exist then you will have to use a program to create a virtual serial port.

### TypeError: a bytes-like object is required, not str

&emsp;&emsp;解决方法将字符串通过`encode`方法转换为`bytes`。如果从网络或磁盘上读取了字节流，那么读到的数据就是`bytes`，要把`bytes`变为`str`，就需要用`decode`方法。
&emsp;&emsp;有时是在处理文件中发生该错误，例如使用`open`函数打开文本文件时，使用了`rb`属性。解决方法是在`open`函数中使用`r`属性，即文本方式读取，而不是`rb`以二进制文件方式读取。

### color.cpp:7456: error: (-215) scn == 3 || scn == 4 in function cv::ipp_cvtColor

&emsp;&emsp;原因是代码中没有对视频是否处理完成做判断，解决方法如下：

``` python
while (cap.isOpened()):
    ret, frame = cap.read()
​
    if not ret:  # 如果不能抓取到一帧，说明我们到了视频的结尾
        print("over!")
        break
    else:
        cv2.imshow('frame', frame)

    if cv2.waitKey(1) & 0xFF == 27:  # 按下ESC键则退出
        break
```

### dict object has no attribute iteritems

&emsp;&emsp;在`Python 3.5`以后，`iteritems`变为`items`。

### 使用pickle读取文件提示TypeError或者UnicodeDecodeError

&emsp;&emsp;使用`python 3`读取`pkl`文件：

``` python
import pickle
train, test, dicts = pickle.load(open("./dataset/atis.pkl"))
```

执行该代码，出现如下错误：

``` python
UnicodeDecodeError: 'ascii' codec can't decode byte 0xe8 in position 0: ordinal not in range(128)
```

我们需要告诉`pickle`：`how to convert Python bytestring data to Python 3 strings, The default is to try and decode all string data as ASCII`，所以代码改为：

``` python
import pickle
train, test, dicts = pickle.load(open("./dataset/atis.pkl", "rb"), encoding='iso-8859-1')
```

`utf-8`编码出现错误也可以使用该方法进行解决。
&emsp;&emsp;或者使用`ignore`对该错误进行忽略：

``` python
train, test, dicts = pickle.load(open("./dataset/atis.pkl", "rb"), encoding='iso-8859-1', errors='ignore')
```

如果这些方法还是解决不了，则考虑修改系统的默认文件编码。`Windows`系统修改文件默认编码的方法：在`设置`中输入`Region`，点击`管理语言设置` -> `更改系统区域设置`，勾选`Beta版：使用Unicode UTF-8提供全球语言支持`。

### AttributeError: _csv.reader object has no attribute next

&emsp;&emsp;原来的代码如下所示：

``` python
allElectronicsData = open('test.csv', 'rb')
reader = csv.reader(allElectronicsData)
headers = reader.next()
```

运行代码就会出现该错误。原因是`python 3`中的`reader`没有了`next`属性，修改如下：

``` python
allElectronicsData = open(r'AllElectronics.csv', 'rb')
reader = csv.reader(allElectronicsData)
headers = next(reader)
```

但运行该代码就会出现`iterator should return strings, not bytes (did you open the file in text mode?)`的错误，解决方法是把`rb`换为`rt`：

``` python
allElectronicsData = open(r'test.csv', 'rt')
reader = csv.reader(allElectronicsData)
headers = next(reader)
```

### ValueError: Expected 2D array, got 1D array instead

&emsp;&emsp;主要是由工具包版本更新造成的，找出出错的两行代码：

``` python
y_train = ss_y.fit_transform(y_train)
y_test = ss_y.transform(y_test)
```

如果数据格式为`[1, 2, 3, 4]`，就会出错；如果把这些数据转换成`[[1], [2], [3], [4]]`，就不会出错了。修改后的代码如下所示：

``` python
y_train = ss_y.fit_transform(y_train.reshape(1, -1))
y_test = ss_y.transform(y_test.reshape(1, -1))
```

如果上述方法出现警告，则采用如下的解决方法：

``` python
y_test = ss_y.transform(numpy.array(y_test).reshape(1, -1))
```

### AttributeError或者DeprecationWarning: This module was deprecated

&emsp;&emsp;在使用`Python`的`sklearn`库时，发现`sklearn`的`cross_validation`不能使用。运行如下程序：

``` python
import sklearn
X, Xt, y, yt = sklearn.cross_validation.train_test_split(X, y)
```

报出如下错误：

``` python
AttributeError: 'module' object has no attribute 'cross_validation'
```

这里尝试修改为：

``` python
from sklearn.cross_validation import train_test_split
X, Xt, y, yt = train_test_split(X, y)
```

发现不再报错，而是提示警告信息：`DeprecationWarning`: This module was deprecated in version `0.18` in favor of the `model_selection` module into which all the refactored classes and functions are moved. Also note that the interface of the new `CV` iterators are different from that of this module. This module will be removed in `0.20`。注意看提示信息，实际上是说`cross_validation`模块被弃用了，改为支持`model_selection`这个模块，因此将程序改为：

``` python
from sklearn.model_selection import train_test_split
X, Xt, y, yt = train_test_split(X, y)
```

### 打印array全部元素

&emsp;&emsp;方法如下所示：

``` python
import numpy as np
np.set_printoptions(threshold=np.inf)
```

### TypeError: object of type zip has no len()

&emsp;&emsp;解决方法是：Try using `list(zip(...))` where you have `zip(...)`。

### AttributeError: module cv2 has no attribute SIFT

&emsp;&emsp;出错的代码如下所示：

``` python
sift = cv2.SIFT()
```

原因是`opencv`将`SIFT`等算法整合到`xfeatures2d`里面了，代码修改如下：

``` python
sift = cv2.xfeatures2d.SIFT_create()
```

同时要注意，安装的`whl`包是`opencv+contrib`。

### Retrying (Retry(total=2, connect=None, read=None, redirect=None))

&emsp;&emsp;通过`pip`安装`scipy`、`scikit-learn`等`python`库时，可能会报出上面的错误，原因是`pip`客户端长时间连接不到`pip`服务器。解决方法是需要将`pip`客户端连接到国内的服务器，以下载`pandas`为例：

``` bash
pip install pandas --index https://pypi.mirrors.ustc.edu.cn/simple/
```

注意，`--index`后面也可以换成别的镜像，比如`http://mirrors.sohu.com/python/`。

### Your CPU supports instructions that this TensorFlow...

&emsp;&emsp;解决方法是加入下面的代码：

``` python
import os
os.environ['TF_CPP_MIN_LOG_LEVEL'] = '2'
```

### No module named _tkinter

&emsp;&emsp;解决方法是使用如下命令：

``` bash
sudo apt-get install python3-tk
```

### pandas的read_csv报错“IOError: Initializing from file failed”

&emsp;&emsp;在`python 3`中使用`read_csv`的时候，如果传入的参数不是文件名而是文件的路径，就会报这个错。解决方法是：先切换到这个目录，然后传文件名作为参数。
&emsp;&emsp;出错代码如下所示：

``` python
import pandas as pd
​
trainFile = "F:/Projects/Python/coursera/train.csv"
trainData = pd.read_csv(trainFile)
```

改进的代码如下：

``` python
import pandas as pd
import os
​
trainFile = "F:/Projects/Python/coursera/train.csv"
​
pwd = os.getcwd()
os.chdir(os.path.dirname(trainFile))
trainData = pd.read_csv(os.path.basename(trainFile))
os.chdir(pwd)
```

### AttributeError: module object has no attribute imread

&emsp;&emsp;解决方法如下：

``` bash
sudo pip install --upgrade Pillow
```

### No module named apt_pkg

&emsp;&emsp;解决方法如下所示：

``` bash
sudo apt-get remove --purge python-apt
sudo apt-get install python-apt -f
sudo find / -name "apt_pkg.cpython-35m-x86_64-linux-gnu.so"
cd /usr/lib/python3/dist-packages/
sudo cp apt_pkg.cpython-35m-x86_64-linux-gnu.so apt_pkg.cpython-36m-x86_64-linux-gnu.so
```

### XXX missing X required positional argument: self

&emsp;&emsp;在`Python`中，类应该先实例化，然后再使用类中的成员。

### Fatal error in launcher: Unable to create process using

&emsp;&emsp;更新`pip`至最新版本即可解决问题：

``` bash
python -m pip install --upgrade pip
```

### OSError: [WinError XXX] 找不到指定的模块/Could not find cudart64_90.dll

&emsp;&emsp;这是因为安装的`TensorFlow`版本和`CUDA`是不是配套的，需要重新安装配套的`TensorFlow`。

### struct.error: argument for XX must be a bytes object

&emsp;&emsp;格式化字符串的值在`Python`中的类型是`bytes`，所以在`bytes`类型前面加上一个`b`就可以解决这个问题：

``` python
import struct
​
a = struct.pack("2I3sI", 12, 34, b"abc", 56)
b = struct.unpack("2I3sI", a)
print(b)  # 结果为(12, 34, b'abc', 56)
```

### TypeError: must be str, not bytes

&emsp;&emsp;写文件的代码`open(filename, 'w').write`应该写为`open(filename, 'wb').write`。

### pycocotools的安装

&emsp;&emsp;`Windows`使用如下命令：

``` bash
pip install git+https://github.com/philferriere/cocoapi.git#subdirectory=PythonAPI
```

`linux`使用如下命令：

``` python
pip install "git+https://github.com/philferriere/cocoapi.git#egg=pycocotools&subdirectory=PythonAPI"
```

然后使用如下命令进行测试：

``` python
from pycocotools.coco import COCO
```

### IndexError: list index out of range

&emsp;&emsp;这个错误的出现可能有两种情况：

- `list[index]`的`index`超出范围。
- `list`是空的，访问`list[0]`就会出现该错误。

### libcudnn.so.6 cannot open shared object file no such file or directory

&emsp;&emsp;使用如下命令即可解决：

``` bash
export LD_LIBRARY_PATH="$LD_LIBRARY_PATH:/usr/local/cuda/lib64"
```

### Data loss: not an sstable (bad magic number): perhaps your file is in a different file

&emsp;&emsp;旧版本的`TensorFlow`在加载模型时，`restore`使用的是模型的路径名，而新版本的`restore`采用的是模型所在的文件夹名称。

### Invalid DISPLAY variable

&emsp;&emsp;I had the same problem and I solved it by adding `plt.switch_backend('agg')` after `import matplotlib.pyplot as plt`.

### Need ffmpeg exe, NeedDownloadError: Need ffmpeg exe

&emsp;&emsp;First, pip install `imageio`, if necessary; then `import imageio` and `imageio.plugins.ffmpeg.download()`.

### from torch._C import * (ImportError: DLL load failed: The specified module could not be found)

&emsp;&emsp;I solved this problem by follow step:

- pip3 uninstall `numpy`
- pip3 install `numpy-1.14.3+mkl-cp36-cp36m-win_amd64.whl` (download from `http://www.lfd.uci.edu/~gohlke/pythonlibs`)

### TypeError: slice indices must be integers or None or have an \_index\_ method

&emsp;&emsp;`python 3`除号`/`的结果是浮点型，而`//`的结果是整型，这一点和`python 2`不一样。

### float object cannot be interpreted as an integer

解决方法同`TypeError: slice indices must be integers or None or have an index method`。

### module object has no attribute xfeatures2d

&emsp;&emsp;使用如下命令解决：

``` bash
pip install opencv-contrib-python
```

### TypeError: unsupported operand type(s) for /: map and int

&emsp;&emsp;`python 2`代码如下：

``` python
datArr = [map(float, line) for line in stringArr]
```

改为`python 3`版本：

``` python
datArr = [list(map(float, line)) for line in stringArr]
```

### OSError: [WinError 10048] 通常每个套接字地址(协议/网络地址/端口)只允许使用一次

&emsp;&emsp;在使用`socket`模块进行服务端和客户端之间的连接时，经常出现如上错误。这是因为进程实际上还未结束，它还占用一个网络端口。解决方法是使用资源管理器将该进程`kill`。

### TypeError: str object is not callable

&emsp;&emsp;`str`是系统自带的函数，你不能在使用它的同时，定义一个叫做`str`的变量，这样就会冲突。

### AttributeError: module pandas has no attribute Factor

&emsp;&emsp;In newer versions of pandas, the `Factor` is called `Categorical` instead. Change your line to:

``` python
df['species'] = pd.Categorical.from_codes(iris.target, iris.target_names)
```

### Python.h：No such file or directory

&emsp;&emsp;如果使用的是`python 3`，输入如下命令即可解决：

``` bash
sudo apt-get install python3-dev
```

### ImportError: No module named ConfigParser

&emsp;&emsp;I found the problem. I had manually installed a newer version of python (version `3.2` but the version installed through packages is `2.7`) and I just redirected `/usr/bin/python` to `python3.2`. Apparently in `3.2` some syntax used in `/usr/bin/pyclean` is not valid anymore. Restoring the original link of `/usr/bin/python` to `/usr/bin/python2` solved the problem. Now the scripts could be run without any issues.

### unexpected keyword argument keepdims

&emsp;&emsp;I got the same error, and I found that according to the TensorFlow docs, the option has to be `keep_dims` instead of `keepdims` in the latest version, and Tensorflow `1.4.1` also.

### unexpected keyword argument serialized_options

&emsp;&emsp;使用如下命令即可解决：

``` python
pip install -U protobuf
```

### 关于“axis = -1”

&emsp;&emsp;The `axis = -1` in numpy corresponds to the last dimension:

``` python
>>> import numpy as np
>>> A = np.array([[[1, 56, 0, 6], [5, 9, 10, 3], [50, 51, 59, 64]],
                  [[2, 6, 4, 3], [10, 80, 53, 6], [12, 5, 36, 15]]])
>>> A.shape
(2, 3, 4)
>>> B = A.argmin(axis = -1)
>>> B.shape
(2, 3)
>>> B
array([[2, 3, 0],
       [0, 3, 1]], dtype=int64)
```

### got an unexpected keyword argument include_top

&emsp;&emsp;Change `include_top` to `require_flatten`, and the issue will be resolved.

### TypeError: softmax() got an unexpected keyword argument axis

&emsp;&emsp;将`keras`的版本改为`2.1.3`即可，使用如下命令：

``` bash
pip install keras==2.1.3
```

或者修改文件`anaconda3/lib/python3.6/site-packages/keras/backend/tensorflow_backend.py`，将`return tf.nn.softmax(x, axis=axis)`改为`return tf.nn.softmax(x)`。

### tensorboard之“ImportError: cannot import name main”

&emsp;&emsp;使用`tensorboard`的时候，可能会出现`ImportError: cannot import name 'main'`的错误，解决方法是将`tensorboard`进行更新。

### dict object has no attribute has_key

&emsp;&emsp;将代码`adict.has_key(key):`改为`key in adict:`。

### 运行Pytorch代码时报错“BrokenPipeError: [Errno 32] Broken pipe”

&emsp;&emsp;该问题的产生是由于`Windows`下的多线程问题，和`DataLoader`类有关。解决方案是修改调用`torch.utils.data.DataLoader`函数时的`num_workers`参数，它是指在进行数据集加载时，启用的线程数目。将其修改为`0`，表示只启用一个主进程加载数据集。

### UserWarning: invalid index of a 0-dim tensor, This will be an error in PyTorch 0.5

&emsp;&emsp;使用`pytorch`进行训练时会出现上述问题，解决方法是将`.data[0]`换成`.item()`，例如将`train_loss += loss.data[0]`变成`train_loss += loss.item()`。

### UserWarning: volatile was removed and now has no effect. Use with torch.no_grad() instead

&emsp;&emsp;解决方法是去掉`Variable`函数中的`volatile=True`，例如将`label = Variable(label.cuda(), volatile=True)`变为`label = Variable(label.cuda())`。

### locale.Error: unsupported locale setting

&emsp;&emsp;在使用`tensorboard`时，可能会出现上述错误，解决方法是在终端输入如下命令：

``` bash
export LANGUAGE=en_US.UTF-8
export LC_ALL=en_US.UTF-8
```

### LookupError: unknown encoding: cp65001

&emsp;&emsp;在使用`Conda`时可能会碰到上述错误，解决方法是使用如下命令：

``` bash
set PYTHONIOENCODING=UTF-8
```

### UnicodeEncodeError: ascii codec can't encode characters in ordinal not in range(128)

&emsp;&emsp;解决方法是设置环境变量`LANG`：

``` bash
export LANG="en_US.UTF-8"
```

### Implicit dimension choice for softmax has been deprecated. Change the call to include dim=X

&emsp;&emsp;原始代码如下所示：

``` python
softmax = F.softmax(enout_att.squeeze(2))
```

需要进行如下修改：

``` python
softmax = F.softmax(enout_att.squeeze(2), dim=1)
```

### 避免KeyError的几种方法

&emsp;&emsp;在读取`dict`的`key`和`value`时，如果`key`不存在，就会触发`KeyError`错误：

``` python
t = {'a': '1', 'b': '2', 'c': '3' }
print(t['d'])
```

&emsp;&emsp;第一种解决方法：首先测试`key`是否存在，然后才进行下一步操作：

``` python
t = {'a': '1', 'b': '2', 'c': '3'}
if 'd' in t:
    print(t['d'])
else:
    print('not exist')  # 输出“not exist”
```

&emsp;&emsp;第二种解决方法：利用`dict`内置的`get(key[, default])`方法，如果`key`存在，返回其`value`，否则返回`default`。使用这个方法永远不会触发`KeyError`：

``` python
t = {'a': '1', 'b': '2', 'c': '3'}
print(t.get('d', 'not exist'))  # 输出“not exist”
print(t)  # 输出“{'a': '1', 'b': '2', 'c': '3'}”
```

&emsp;&emsp;第三种解决方法：利用`dict`内置的`setdefault(key[, default])`方法，如果`key`存在，返回其`value`，否则插入此`key`，其`value`为`default`，并返回`default`。使用这个方法也永远不会触发`KeyError`：

``` python
t = {'a': '1', 'b': '2', 'c': '3'}
print(t.setdefault('d'))  # 输出“None”
print(t)  # 输出“{'a': '1', 'b': '2', 'c': '3', 'd': None}”
```

### ValueError: The truth value of an array with more than one element is ambiguous. Use a.any() or a.all()

&emsp;&emsp;原来的代码如下所示：

``` python
import numpy as np
​
a = np.zeros(3)
a[0] = 0
a[1] = 1
a[2] = 2
​
if a == [1, 2, 3]:
    print("OK")
else:
    print("NOT OK")
```

`Numpy`可以认为等号两边两个式子是数值不相等，返回`False`；也可以认为等号两边两个式子是逻辑相等，返回`True`。它觉得这是模棱两可的，因此放弃做判断。最好统一用`any`进行`或比较`，或者用`all`进行`与比较`。

``` python
import numpy as np
​
a = np.zeros(3)
a[0] = 0
a[1] = 1
a[2] = 2
print((a == [0, 1, 2]).any())  # True
print((a == [0, 1, 2]).all())  # True
print((a == [1, 2, 3]).any())  # False
print((a == [1, 2, 3]).all())  # False
print((a == [0, 2, 3]).any())  # True
print((a == [0, 2, 3]).all())  # False
```

### 添加PYTHONPATH

&emsp;&emsp;`Linux`或`Mac`系统添加`PYTHONPATH`的方法：在`.bashrc`(或`.bash_profile`)中添加如下内容：

``` bash
export PYTHONPATH="待加入路径:{$PYTHONPATH}"
```

然后使用命令`source .bashrc`。

### Default value of block_norm == L1 is deprecated and will be changed to L2-Hys in v0.15

&emsp;&emsp;需要对代码进行如下修改：

``` python
hog(..., block_norm='L2-Hys')
```

### ValueError: Selected block normalization method is invalid

&emsp;&emsp;The definition of function hog is as follows:

``` python
def hog(
    image, orientations=9, pixels_per_cell=(8, 8), cells_per_block=(3, 3),
    block_norm='L1', visualise=False, transform_sqrt=False,
    feature_vector=True, normalise=None)
```

so removing `normalize` and `visualise` will works.