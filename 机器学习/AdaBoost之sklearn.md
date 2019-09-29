---
title: AdaBoost之sklearn
categories: 机器学习
abbrlink: ec98f519
date: 2019-02-12 09:55:30
---
&emsp;&emsp;`sklearn.ensemble`模块提供了很多集成方法，例如`AdaBoost`、`Bagging`、随机森林等：

方法                                                | 说明
----------------------------------------------------|------
`ensemble.AdaBoostClassifier([...])`                | An `AdaBoost` classifier
`ensemble.AdaBoostRegressor([base_estimator, ...])` | An `AdaBoost` regressor
`ensemble.BaggingClassifier([base_estimator, ...])` | A `Bagging` classifier
`ensemble.BaggingRegressor([base_estimator, ...])`  | A `Bagging` regressor
`ensemble.ExtraTreesClassifier([...])`              | An `extra-trees` classifier
`ensemble.ExtraTreesRegressor([n_estimators, ...])` | An `extra-trees` regressor
`ensemble.GradientBoostingClassifier([loss, ...])`  | `Gradient Boosting` for classification
`ensemble.GradientBoostingRegressor([loss, ....])`  | `Gradient Boosting` for regression
`ensemble.IsolationForest([n_estimators, ...])`     | `Isolation Forest Algorithm`
`ensemble.RandomForestClassifier([...])`            | A random forest classifier
`ensemble.RandomForestRegressor([...])`             | A random forest regressor
`ensemble.RandomTreesEmbedding([...])`              | An ensemble of totally random trees
`ensemble.VotingClassifier(estimators[, ...])`      | `Soft Voting/Majority Rule` classifier for unfitted estimators

&emsp;&emsp;`sklearn.ensemble.AdaBoostClassifier`函数原型如下：

``` python
class sklearn.ensemble.AdaBoostClassifier(
    base_estimator=None, n_estimators=50, learning_rate=1.0,
    algorithm='SAMME.R', random_state=None)
```

- `base_estimator`：可选参数，默认为`DecisionTreeClassifier`。理论上可以选择任何一个分类或者回归学习器，不过需要支持样本权重。我们常用的一般是`CART`决策树或者神经网络`MLP`。默认是决策树，即`AdaBoostClassifier`默认使用`CART`分类树`DecisionTreeClassifier`，而`AdaBoostRegressor`默认使用`CART`回归树`DecisionTreeRegressor`。
- `algorithm`：可选参数，默认为`SAMME.R`。`scikit-learn`实现了两种`Adaboost`分类算法，即`SAMME`和`SAMME.R`。两者的主要区别是弱学习器权重的度量，`SAMME`使用对样本集分类效果作为弱学习器权重，而`SAMME.R`使用了对样本集分类的预测概率大小来作为弱学习器权重。由于`SAMME.R`使用了概率度量的连续值，迭代一般比`SAMME`快，因此`AdaBoostClassifier`的默认算法`algorithm`的值也是`SAMME.R`。我们一般使用默认的`SAMME.R`就够了，但是要注意的是使用了`SAMME.R`，则弱分类学习器参数`base_estimator`必须限制使用支持概率预测的分类器。`SAMME`算法则没有这个限制。
- `n_estimators`：整数型，可选参数，默认为`50`。弱学习器的最大迭代次数，或者说最大的弱学习器的个数。一般来说`n_estimators`太小，容易欠拟合；`n_estimators`太大，又容易过拟合，一般使用默认值即可。在实际调参的过程中，我们常常将`n_estimators`和下面介绍的参数`learning_rate`一起考虑。
- `learning_rate`：浮点型，可选参数，默认为`1.0`。每个弱学习器的权重缩减系数，取值范围为`0`到`1`。对于同样的训练集拟合效果，较小的`learning_rate`意味着需要更多的弱学习器迭代次数。通常我们用步长和迭代最大次数一起来决定算法的拟合效果，所以`n_estimators`和`learning_rate`这两个参数要一起调参。一般来说，可以从一个小的`learning_rate`开始调参。
- `random_state`：整数型，可选参数，默认为`None`。If `int`, `random_state` is the seed used by the random number generator; If `RandomState` instance, `random_state` is the random number generator; If `None`, the random number generator is the `RandomState` instance used by `np.random`.

&emsp;&emsp;`AdaBoostClassifier`提供了如下方法：

方法                                  | 说明
--------------------------------------|------
`decision_function(X)`                | Compute the decision function of `X`
`fit(X, y[, sample_weight])`          | Build a boosted classifier from the training set `(X, y)`
`get_params([deep])`                  | Get parameters for this estimator
`predict(X)`                          | Predict classes for `X`
`predict_log_proba(X)`                | Predict class `log-probabilities` for `X`
`predict_proba(X)`                    | Predict class probabilities for `X`
`score(X, y[, sample_weight])`        | Returns the mean accuracy on the given test data and labels
`set_params(**params)`                | Set the parameters of this estimator
`staged_decision_function(X)`         | Compute decision function of `X` for each boosting iteration
`staged_predict(X)`                   | Return staged predictions for `X`
`staged_predict_proba(X)`             | Predict class probabilities for `X`
`staged_score(X, y[, sample_weight])` | Return staged scores for `X, y`

&emsp;&emsp;预测病马死亡率的代码如下：

``` python
import numpy as np
from sklearn.ensemble import AdaBoostClassifier
from sklearn.tree import DecisionTreeClassifier
​
def loadDataSet(fileName):
    numFeat = len((open(fileName).readline().split('\t')))
    dataMat = []
    labelMat = []
    fr = open(fileName)

    for line in fr.readlines():
        lineArr = []
        curLine = line.strip().split('\t')

        for i in range(numFeat - 1):
            lineArr.append(float(curLine[i]))

        dataMat.append(lineArr)
        labelMat.append(float(curLine[-1]))

    return dataMat, labelMat
​
if __name__ == '__main__':
    dataArr, classLabels = loadDataSet('horseColicTraining2.txt')
    testArr, testLabelArr = loadDataSet('horseColicTest2.txt')
    bdt = AdaBoostClassifier(DecisionTreeClassifier(max_depth=2), algorithm="SAMME", n_estimators=10)
    bdt.fit(dataArr, classLabels)
    predictions = bdt.predict(dataArr)
    errArr = np.mat(np.ones((len(dataArr), 1)))
    print('训练集的错误率: %.3f%%' % float(errArr[predictions != classLabels].sum() / len(dataArr) * 100))
    predictions = bdt.predict(testArr)
    errArr = np.mat(np.ones((len(testArr), 1)))
    print('测试集的错误率: %.3f%%' % float(errArr[predictions != testLabelArr].sum() / len(testArr) * 100))
```

执行结果：

``` python
训练集的错误率: 16.054%
测试集的错误率: 17.910%
```

我们使用`DecisionTreeClassifier`作为使用的弱分类器，使用`AdaBoost`算法训练分类器。可以看到训练集的错误率为`16.054%`，测试集的错误率为`17.910%`。注意，如果`n_enstimators`参数过大，会导致过拟合。