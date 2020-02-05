---
title: K近邻算法sklearn
categories: 机器学习
abbrlink: '47596074'
date: 2019-02-12 11:28:15
---
&emsp;&emsp;`sklearn.neighbors`模块实现了`k`近邻算法：<!--more-->

方法                                                | 说明
----------------------------------------------------|-----
`neighbors.NearestNeighbors([n_neighbors, ...])`    | Unsupervised learner for implementing neighbor searches
`neighbors.KNeighborsClassifier([...])`             | Classifier implementing the `k-nearest` neighbors vote
`neighbors.RadiusNeighborsClassifier([...])`        | Classifier implementing a vote among neighbors within a given radius
`neighbors.KNeighborsRegressor([n_neighbors, ...])` | Regression based on `k-nearest` neighbors
`neighbors.RadiusNeighborsRegressor([radius, ...])` | Regression based on neighbors within a fixed `radius`
`neighbors.NearestCentroid([metric, ...])`          | Nearest centroid classifier
`neighbors.BallTree`                                | `BallTree` for fast generalized `N-point` problems
`neighbors.KDTree`                                  | `KDTree` for fast generalized `N-point` problems
`neighbors.LSHForest([n_estimators, radius, ...])`  | Performs approximate nearest neighbor search using `LSH` forest
`neighbors.DistanceMetric`                          | DistanceMetric class
`neighbors.KernelDensity([bandwidth, ...])`         | `Kernel Density Estimation`
`neighbors.kneighbors_graph(X, n_neighbors[, ...])` | Computes the (weighted) graph of `k-Neighbors` for points in `X`
`neighbors.radius_neighbors_graph(X, radius)`       | Computes the (weighted) graph of `Neighbors` for points in `X`

使用`sklearn.neighbors.KNeighborsClassifier`就可以实现k近邻算法：

``` python
class sklearn.neighbors.KNeighborsClassifier(
    n_neighbors=5, weights="uniform", algorithm="auto", leaf_size=30,
    p=2, metric="minkowski", metric_params=None, n_jobs=1, **kwargs)
```

- `n_neighbors`：默认为`5`，就是`kNN`的`k`的值，选取最近的k个点。
- `weights`：默认是`uniform`，参数可以是`uniform`、`distance`，也可以是用户自己定义的函数。`uniform`是均等的权重，就说所有的邻近点的权重都是相等的；`distance`是不均等的权重，距离近的点比距离远的点的影响大。如果是用户自定义的函数，则接收距离的数组，返回一组维数相同的权重。
- `algorithm`：快速`k`近邻搜索算法，默认参数为`auto`，可以理解为算法自己决定合适的搜索算法。除此之外，用户也可以自己指定搜索算法，例如`ball_tree`、`kd_tree`、`brute`：`brute`是蛮力搜索，也就是线性扫描，当训练集很大时，计算非常耗时；对于`kd_tree`，构造`kd`树存储数据以便对其进行快速检索的树形数据结构，`kd`树也就是数据结构中的二叉树，以中值切分构造的树，每个结点是一个超矩形，在维数小于`20`时效率高；`ball_tree`是为了克服`kd`树高维失效而发明的，其构造过程是以质心`C`和半径`r`分割样本空间，每个节点是一个超球体。
- `leaf_size`：默认是`30`，这个是构造的`kd`树和`ball`树的大小。这个值的设置会影响树构建的速度和搜索速度，同样也影响着存储树所需的内存大小。需要根据问题的性质选择最优的大小。
- `metric`：用于距离度量，默认度量是`minkowski`，也就是`p = 2`的欧氏距离(欧几里德度量)。
- `p`：距离度量公式。可以使用欧氏距离公式进行距离度量，除此之外还有其他的度量方法，例如曼哈顿距离。这个参数默认为`2`，也就是默认使用欧式距离公式进行距离度量。也可以设置为`1`，使用曼哈顿距离公式进行距离度量。
- `metric_params`：距离公式的其他关键参数，这个可以不管，使用默认的`None`即可。
- `n_jobs`：并行处理设置。默认为`1`，临近点搜索并行工作数。如果为`-1`，那么`CPU`的所有`cores`都用于并行工作。

&emsp;&emsp;`KNeighborsClassifier`提供了一些方法供我们使用：

方法                                            | 说明
------------------------------------------------|-----
`fit(X, y)`                                     | Fit the model using `X` as training data and `y` as target values
`get_params([deep])`                            | Get parameters for this estimator
`kneighbors([X, n_neighbors, return_distance])` | Finds the `K-neighbors` of a point
`kneighbors_graph([X, n_neighbors, mode])`      | Computes the (weighted) graph of `k-Neighbors` for points in `X`
`predict(X)`                                    | Predict the class labels for the provided data
`predict_proba(X)`                              | Return probability estimates for the test data `X`
`score(X, y[, sample_weight])`                  | Returns the mean accuracy on the given test data and labels
`set_params(**params)`                          | Set the parameters of this estimator

&emsp;&emsp;代码一如下：

``` python
from sklearn import neighbors
from sklearn import datasets
​
knn = neighbors.KNeighborsClassifier()
iris = datasets.load_iris()
print(iris)
knn.fit(iris.data, iris.target)
predictedLabel = knn.predict([[0.1, 0.2, 0.3, 0.4]])
print(predictedLabel)
```

&emsp;&emsp;不使用`sklearn`的`KNN`算法如下：

``` python
import csv
import random
import math
import operator
​
def loadDataset(filename, split, trainingSet=[], testSet=[]):
    with open(filename, 'rt') as csvfile:
        lines = csv.reader(csvfile)
        dataset = list(lines)

        for x in range(len(dataset) - 1):
            for y in range(4):
                dataset[x][y] = float(dataset[x][y])

            if random.random() < split:
                trainingSet.append(dataset[x])
            else:
                testSet.append(dataset[x])
​
def euclideanDistance(instance1, instance2, length):
    distance = 0

    for x in range(length):
        distance += pow((instance1[x] - instance2[x]), 2)

    return math.sqrt(distance)
​
def getNeighbors(trainingSet, testInstance, k):
    distances = []
    length = len(testInstance) - 1

    for x in range(len(trainingSet)):
        dist = euclideanDistance(testInstance, trainingSet[x], length)
        distances.append((trainingSet[x], dist))

    distances.sort(key=operator.itemgetter(1))
    neighbors = []

    for x in range(k):
        neighbors.append(distances[x][0])

    return neighbors
​
def getResponse(neighbors):
    classVotes = {}

    for x in range(len(neighbors)):
        response = neighbors[x][-1]

        if response in classVotes:
            classVotes[response] += 1
        else:
            classVotes[response] = 1

    sortedVotes = sorted(classVotes.items(), key=operator.itemgetter(1), reverse=True)
    return sortedVotes[0][0]
​
def getAccuracy(testSet, predictions):
    correct = 0

    for x in range(len(testSet)):
        if testSet[x][-1] == predictions[x]:
            correct += 1

    return (correct / float(len(testSet))) * 100.0
​
def main():
    trainingSet = []
    testSet = []
    split = 0.67
    loadDataset(r'irisdata.txt', split, trainingSet, testSet)
    print('Train set: ' + repr(len(trainingSet)))
    print('Test set: ' + repr(len(testSet)))
    predictions = []
    k = 3

    for x in range(len(testSet)):
        neighbors = getNeighbors(trainingSet, testSet[x], k)
        result = getResponse(neighbors)
        predictions.append(result)
        print('>predicted=' + repr(result) + ', actual=' + repr(testSet[x][-1]))

    print('predictions: ' + repr(predictions))
    accuracy = getAccuracy(testSet, predictions)
    print('Accuracy: ' + repr(accuracy) + '%')
​
if __name__ == '__main__':
    main()
```

---

&emsp;&emsp;本次测试就使用`Python`自带的`iris`数据集，这个数据集里面有`150`个实例，每个实例里有如下`4`个特征值如下：萼片长度(`sepal length`)、萼片宽度(`sepal width`)、花盘长度(`petal length`)、花盘宽度(`petal width`)。类别(`label`)有如下三种：`Iris setosa`、`Iris versicolor`和`Iris virginica`。

``` python
from sklearn.datasets import load_iris
from sklearn import neighbors, metrics
from sklearn.model_selection import train_test_split
​
knn = neighbors.KNeighborsClassifier(n_neighbors=5)  # 实例化KNN对象，选择K为5
iris = load_iris()  # 加载iris数据集
print(iris)
​
# 对数据集进行切割分类，分别为训练数据、测试数据、训练标记、测试标记，比例是“4:1”。
# random_state设置为零可以保证每次的随机数是一样的。如果是1，每次结果都不一样
train_data, test_data, train_target, test_target = \
    train_test_split(iris.data, iris.target, test_size=0.2, random_state=0)
​
knn.fit(train_data, train_target)  # 建立模型
print(knn)
print(knn.classes_)  # 打印种类
print(iris.target_names)  # 打印三类花的名字
​
test_res = knn.predict(test_data)  # 开始预测
# 打印准确的标记和预测的标记
print(test_target)
print(test_res)
print(metrics.accuracy_score(test_res, test_target))  # 打印预测准确率
```