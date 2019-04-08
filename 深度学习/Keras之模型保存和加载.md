---
title: Keras之模型保存和加载
date: 2019-01-01 14:04:48
categories: 深度学习
---
&emsp;&emsp;不建议使用`pickle`或`cPickle`来保存`Keras`模型。你可以使用`model.save(filepath)`将`Keras`模型保存到单个`HDF5`文件中，该文件将包含：

- 模型的结构，允许重新创建模型。
- 模型的权重。
- 训练配置项(损失函数和优化器)。
- 优化器状态，允许准确地从你上次结束的地方继续训练。

你可以使用`keras.models.load_model(filepath)`重新实例化模型。`load_model`还将负责使用保存的训练配置项来编译模型(除非模型从未编译过)。

``` python
from keras.models import load_model
​
model.save('my_model.h5')  # 创建HDF5文件“my_model.h5”
del model  # 删除现有模型
model = load_model('my_model.h5')  # 返回一个编译好的模型，与之前那个相同
```

示例代码如下：

``` python
import numpy as np
from keras.models import Sequential
from keras.layers import Dense
from keras.models import load_model
​
np.random.seed(1337)  # for reproducibility
X = np.linspace(-1, 1, 200)  # create some data
np.random.shuffle(X)  # randomize the data
Y = 0.5 * X + 2 + np.random.normal(0, 0.05, (200,))
X_train, Y_train = X[:160], Y[:160]  # first 160 data points
X_test, Y_test = X[160:], Y[160:]  # last 40 data points
​
model = Sequential()
model.add(Dense(output_dim=1, input_dim=1))
model.compile(loss='mse', optimizer='sgd')
​
for step in range(301):
    cost = model.train_on_batch(X_train, Y_train)
​
# save
print('test before save: ', model.predict(X_test[0:2]))
model.save('my_model.h5')  # HDF5 file, you have to pip3 install h5py if don't have it
del model  # deletes the existing model
​
# load
model = load_model('my_model.h5')
print('test after load: ', model.predict(X_test[0:2]))
```

### 只保存/加载模型的结构

&emsp;&emsp;如果你只需要保存模型的结构，而非其权重或训练配置项，则可以执行以下操作：

``` python
json_string = model.to_json() # 保存为JSON
yaml_string = model.to_yaml() # 保存为YAML
```

生成的`JSON`或`YAML`文件是人类可读的，如果需要的话还可以手动编辑。
&emsp;&emsp;你可以从这些数据建立一个新的模型：

``` python
# 从JSON重建模型：
from keras.models import model_from_json
model = model_from_json(json_string)
# 从YAML重建模型：
from keras.models import model_from_yaml
model = model_from_yaml(yaml_string)
```

### 只保存/加载模型的权重

&emsp;&emsp;如果您只需要模型的权重，可以使用下面的代码以`HDF5`格式进行保存。请注意，首先需要安装`HDF5`的`Python`库`h5py`，它不包含在`Keras`中：

``` python
model.save_weights('my_model_weights.h5')
```

假设你有用于实例化模型的代码，则可以将保存的权重加载到具有相同结构的模型中：

``` python
model.load_weights('my_model_weights.h5')
```

如果你需要将权重加载到不同的结构(有一些共同层)的模型中，例如`fine-tune`或`transfer-learning`，则可以按层的名字来加载权重：

``` python
model.load_weights('my_model_weights.h5', by_name=True)
```

实例如下：

``` python
"""
假设原始模型如下所示：
model = Sequential()
model.add(Dense(2, input_dim=3, name='dense_1'))
model.add(Dense(3, name='dense_2'))
...
model.save_weights(fname)
"""
model = Sequential()  # 新模型
model.add(Dense(2, input_dim=3, name='dense_1'))  # 将被加载
model.add(Dense(10, name='new_dense'))  # 将不被加载
model.load_weights(fname, by_name=True)  # 从第一个模型加载权重，只会影响第一层(dense_1)
```