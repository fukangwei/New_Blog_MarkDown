---
title: TensorBoard的使用
categories: 深度学习
date: 2019-02-16 12:08:29
---
### keras和TensorBoard训练可视化

&emsp;&emsp;代码如下：

``` python
import numpy as np
from keras.datasets import mnist
from keras.models import Sequential
from keras.layers.core import Dense, Dropout, Activation
from keras.optimizers import SGD
from keras.utils import np_utils
import keras.callbacks
import keras.backend.tensorflow_backend as KTF
import tensorflow as tf

batch_size = 128
nb_classes = 10
nb_epoch = 20
nb_data = 28 * 28
log_filepath = './log_test'

(X_train, y_train), (X_test, y_test) = mnist.load_data()  # load data

# reshape
X_train = X_train.reshape(X_train.shape[0], X_train.shape[1] * X_train.shape[2])
X_test = X_test.reshape(X_test.shape[0], X_test.shape[1] * X_test.shape[2])

# rescale
X_train = X_train.astype(np.float32)
X_test = X_test.astype(np.float32)
X_train /= 255
X_test /= 255

# convert class vectors to binary class matrices (one hot vectors)
Y_train = np_utils.to_categorical(y_train, nb_classes)
Y_test = np_utils.to_categorical(y_test, nb_classes)

old_session = KTF.get_session()

with tf.Graph().as_default():
    session = tf.Session('')
    KTF.set_session(session)
    KTF.set_learning_phase(1)
    # build model
    model = Sequential()
    model.add(Dense(512, input_shape=(nb_data,), init='normal', name='dense1'))
    model.add(Activation('relu', name='relu1'))
    model.add(Dropout(0.2, name='dropout1'))
    model.add(Dense(512, init='normal', name='dense2'))
    model.add(Activation('relu', name='relu2'))
    model.add(Dropout(0.2, name='dropout2'))
    model.add(Dense(10, init='normal', name='dense3'))
    model.add(Activation('softmax', name='softmax1'))
    model.summary()

    model.compile(loss='categorical_crossentropy', optimizer=SGD(lr=0.01), metrics=['accuracy'])

    tb_cb = keras.callbacks.TensorBoard(log_dir=log_filepath, histogram_freq=1)
    cbks = [tb_cb]

    history = model.fit(
                X_train, Y_train, batch_size=batch_size, nb_epoch=nb_epoch,
                verbose=1, callbacks=cbks, validation_data=(X_test, Y_test))

    score = model.evaluate(X_test, Y_test, verbose=0)
    print('Test score:', score[0])
    print('Test accuracy;', score[1])

KTF.set_session(old_session)
```

在终端使用如下命令：

``` python
tensorboard --logdir ./log_test
```


---

&emsp;&emsp;`pytorch`使用`tensorboardX`添加数据的方法：

``` python
import torch
import torchvision.utils as vutils
import numpy as np
import torchvision.models as models
from torchvision import datasets
from tensorboardX import SummaryWriter

resnet18 = models.resnet18(False)
writer = SummaryWriter()
sample_rate = 44100
freqs = [262, 294, 330, 349, 392, 440, 440, 440, 440, 440, 440]

for n_iter in range(100):
    dummy_s1 = torch.rand(1)
    dummy_s2 = torch.rand(1)
    # data grouping by slash
    writer.add_scalar('data/scalar1', dummy_s1[0], n_iter)
    writer.add_scalar('data/scalar2', dummy_s2[0], n_iter)
    writer.add_scalars(
        'data/scalar_group',
        {
            'xsinx': n_iter * np.sin(n_iter),
            'xcosx': n_iter * np.cos(n_iter),
            'arctanx': np.arctan(n_iter)
        },
        n_iter)

    dummy_img = torch.rand(32, 3, 64, 64)  # output from network

    if n_iter % 10 == 0:
        x = vutils.make_grid(dummy_img, normalize=True, scale_each=True)
        writer.add_image('Image', x, n_iter)

        dummy_audio = torch.zeros(sample_rate * 2)

        for i in range(x.size(0)):
            # amplitude of sound should in [-1, 1]
            dummy_audio[i] = np.cos(freqs[n_iter // 10] * np.pi * float(i) / float(sample_rate))

        writer.add_audio('myAudio', dummy_audio, n_iter, sample_rate=sample_rate)
        writer.add_text('Text', 'text logged at step:' + str(n_iter), n_iter)

        for name, param in resnet18.named_parameters():
            writer.add_histogram(name, param.clone().cpu().data.numpy(), n_iter)

        # needs tensorboard 0.4RC or later
        writer.add_pr_curve('xoxo', np.random.randint(2, size=100), np.random.rand(100), n_iter)

dataset = datasets.MNIST('mnist', train=False, download=True)
images = dataset.test_data[:100].float()
label = dataset.test_labels[:100]

features = images.view(100, 784)
writer.add_embedding(features, metadata=label, label_img=images.unsqueeze(1))
# export scalar data to JSON for external processing
writer.export_scalars_to_json("./all_scalars.json")
writer.close()
```

执行完成之后，在终端输入`tensorboard.exe --logdir runs`，注意当前`python`文件和执行`tensorboard`的当前路径。画出神经网络结构的方法如下所示(注意`add_graph`的第二个参数应该是个`Tensor`)：

``` python
import torch
import torch.nn as nn
import torch.nn.functional as F
import torchvision
from torch.autograd import Variable
from tensorboardX import SummaryWriter

dummy_input = (torch.zeros(1, 3),)

class LinearLanpa(nn.Module):
    def __init__(self):
        super(LinearLanpa, self).__init__()
        self.l = nn.Linear(3, 5)

    def forward(self, x):
        return self.l(x)

with SummaryWriter(comment='LinearModel') as w:
    w.add_graph(LinearLanpa(), dummy_input, True)
# -----------------------------
class SimpleModel(nn.Module):
    def __init__(self):
        super(SimpleModel, self).__init__()

    def forward(self, x):
        return x * 2

model = SimpleModel()
dummy_input = (torch.zeros(1, 2, 3),)

with SummaryWriter(comment='constantModel') as w:
    w.add_graph(model, dummy_input, True)
# -----------------------------
def conv3x3(in_planes, out_planes, stride=1):
    # 3x3 convolution with padding
    return nn.Conv2d(in_planes, out_planes, kernel_size=3, stride=stride, padding=1, bias=False)

class BasicBlock(nn.Module):
    def __init__(self, inplanes, planes, stride=1, downsample=None):
        super(BasicBlock, self).__init__()
        self.conv1 = conv3x3(inplanes, planes, stride)
        self.bn1 = nn.BatchNorm2d(planes)
        self.conv2 = conv3x3(planes, planes)
        self.bn2 = nn.BatchNorm2d(planes)
        self.stride = stride

    def forward(self, x):
        residual = x
        out = self.conv1(x)
        out = self.bn1(out)
        out = F.relu(out)
        out = self.conv2(out)
        out = self.bn2(out)
        out += residual
        out = F.relu(out)
        return out

dummy_input = torch.rand(1, 3, 224, 224)

with SummaryWriter(comment='basicblock') as w:
    model = BasicBlock(3, 3)
    w.add_graph(model, (dummy_input,), verbose=True)
# -----------------------------
class Net1(nn.Module):
    def __init__(self):
        super(Net1, self).__init__()
        self.conv1 = nn.Conv2d(1, 10, kernel_size=5)
        self.conv2 = nn.Conv2d(10, 20, kernel_size=5)
        self.conv2_drop = nn.Dropout2d()
        self.fc1 = nn.Linear(320, 50)
        self.fc2 = nn.Linear(50, 10)
        self.bn = nn.BatchNorm2d(20)

    def forward(self, x):
        x = F.max_pool2d(self.conv1(x), 2)
        x = F.relu(x) + F.relu(-x)
        x = F.relu(F.max_pool2d(self.conv2_drop(self.conv2(x)), 2))
        x = self.bn(x)
        x = x.view(-1, 320)
        x = F.relu(self.fc1(x))
        x = F.dropout(x, training=self.training)
        x = self.fc2(x)
        x = F.softmax(x, dim=1)
        return x

class Net2(nn.Module):
    def __init__(self):
        super(Net2, self).__init__()
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
        x = F.log_softmax(x, dim=1)
        return x

dummy_input = Variable(torch.rand(13, 1, 28, 28))

model = Net1()

with SummaryWriter(comment='Net1') as w:
    w.add_graph(model, (dummy_input,))

model = Net2()

with SummaryWriter(comment='Net2') as w:
    w.add_graph(model, (dummy_input,))

dummy_input = torch.Tensor(1, 3, 224, 224)

with SummaryWriter(comment='alexnet') as w:
    model = torchvision.models.alexnet()
    w.add_graph(model, (dummy_input,))

with SummaryWriter(comment='vgg19') as w:
    model = torchvision.models.vgg19()
    w.add_graph(model, (dummy_input,))

with SummaryWriter(comment='densenet121') as w:
    model = torchvision.models.densenet121()
    w.add_graph(model, (dummy_input,))

with SummaryWriter(comment='resnet18') as w:
    model = torchvision.models.resnet18()
    w.add_graph(model, (dummy_input,))
# -----------------------------
class RNN(nn.Module):
    def __init__(self, input_size, hidden_size, output_size):
        super(RNN, self).__init__()
        self.hidden_size = hidden_size
        self.i2h = nn.Linear(n_categories + input_size + hidden_size, hidden_size)
        self.i2o = nn.Linear(n_categories + input_size + hidden_size, output_size)
        self.o2o = nn.Linear(hidden_size + output_size, output_size)
        self.dropout = nn.Dropout(0.1)
        self.softmax = nn.LogSoftmax(dim=1)

    def forward(self, category, input, hidden):
        input_combined = torch.cat((category, input, hidden), 1)
        hidden = self.i2h(input_combined)
        output = self.i2o(input_combined)
        output_combined = torch.cat((hidden, output), 1)
        output = self.o2o(output_combined)
        output = self.dropout(output)
        output = self.softmax(output)
        return output, hidden

    def initHidden(self):
        return torch.zeros(1, self.hidden_size)

n_letters = 100
n_hidden = 128
n_categories = 10
rnn = RNN(n_letters, n_hidden, n_categories)
cat = torch.Tensor(1, n_categories)
dummy_input = torch.Tensor(1, n_letters)
hidden = torch.Tensor(1, n_hidden)

out, hidden = rnn(cat, dummy_input, hidden)

with SummaryWriter(comment='RNN') as w:
    w.add_graph(rnn, (cat, dummy_input, hidden), verbose=False)
```