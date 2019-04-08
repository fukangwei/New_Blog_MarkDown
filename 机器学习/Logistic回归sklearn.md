---
title: Logistic回归sklearn
date: 2019-02-12 14:04:32
tags:
---
&emsp;&emsp;`sklearn.linear_model`模块提供了很多模型供我们使用，比如`Logistic`回归、`Lasso`回归、贝叶斯脊回归等：

方法                                                | 说明
----------------------------------------------------|------
`linear_model.ARDRegression([n_iter, tol, ...])`    | `Bayesian ARD regression`
`linear_model.BayesianRidge([n_iter, tol, ...])`    | `Bayesian ridge regression`
`linear_model.ElasticNet([alpha, l1_ratio, ...])`   | `Linear regression` with combined `L1` and `L2` priors as regularizer
`linear_model.ElasticNetCV([l1_ratio, eps, ...])`   | `Elastic Net model` with iterative fitting along a regularization path
`linear_model.HuberRegressor([epsilon, ...])`       | `Linear regression` model that is robust to outliers
`linear_model.Lars([fit_intercept, verbose, ...])`  | `Least Angle Regression` model `a.k.a`
`linear_model.LarsCV([fit_intercept, ...])`         | `Cross-validated Least Angle Regression` model
`linear_model.Lasso([alpha, fit_intercept, ...])`   | `Linear Model` trained with `L1` prior as regularizer
`linear_model.LassoCV([eps, n_alphas, ...])`        | `Lasso` linear model with iterative fitting along a regularization path
`linear_model.LassoLars([alpha, ...])`              | `Lasso` model fit with `Least Angle Regression`
`linear_model.LassoLarsCV([fit_intercept, ...])`    | `Cross-validated Lasso`, using the `LARS` algorithm
`linear_model.LassoLarsIC([criterion, ...])`        | `Lasso` model fit with `Lars` using `BIC` or `AIC` for model selection
`linear_model.LinearRegression([...])`              | Ordinary least squares `Linear Regression`
`linear_model.LogisticRegression([penalty, ...])`   | `Logistic Regression` (`aka logit`, `MaxEnt`) classifier
`linear_model.LogisticRegressionCV([Cs, ...])`      | `Logistic Regression CV` (`aka logit`, `MaxEnt`) classifier
`linear_model.MultiTaskLasso([alpha, ...])`         | `Multi-task Lasso` model trained with `L1/L2` `mixed-norm` as regularizer
`linear_model.MultiTaskElasticNet([alpha, ...])`    | `Multi-task ElasticNet` model trained with `L1/L2` `mixed-norm` as regularizer
`linear_model.MultiTaskLassoCV([eps, ...])`         | `Multi-task L1/L2 Lasso` with `built-in` `cross-validation`
`linear_model.MultiTaskElasticNetCV([...])`         | `Multi-task L1/L2 ElasticNet` with `built-in` `cross-validation`
`linear_model.OrthogonalMatchingPursuit([...])`     | `Orthogonal Matching Pursuit` model
`linear_model.OrthogonalMatchingPursuitCV([...])`   | `Cross-validated` `Orthogonal Matching Pursuit` model
`linear_model.PassiveAggressiveClassifier([...])`   | `Passive Aggressive Classifier`
`linear_model.PassiveAggressiveRegressor([C, ...])` | `Passive Aggressive Regressor`
`linear_model.RANSACRegressor([...])`               | `RANSAC` (`RANdom SAmple Consensus`) algorithm
`linear_model.Ridge([alpha, fit_intercept, ...])`   | `Linear` least squares with `l2` regularization
`linear_model.RidgeClassifier([alpha, ...])`        | Classifier using `Ridge` regression
`linear_model.RidgeClassifierCV([alphas, ...])`     | Ridge classifier with `built-in` `cross-validation`
`linear_model.RidgeCV([alphas, ...])`               | Ridge regression with `built-in` `cross-validation`
`linear_model.SGDClassifier([loss, penalty, ...])`  | `Linear` classifiers (`SVM`, `logistic regression`) with `SGD` training
`linear_model.SGDRegressor([loss, penalty, ...])`   | `Linear` model fitted by minimizing a regularized empirical loss with `SGD`
`linear_model.TheilSenRegressor([...])`             | `Theil-Sen Estimator`: robust multivariate regression model
`linear_model.enet_path(X, y[, l1_ratio, ...])`     | Compute elastic net path with coordinate descent
`linear_model.lars_path(X, y[, Xy, Gram, ...])`     | Compute `Least Angle Regression` or `Lasso` path using `LARS` algorithm
`linear_model.lasso_path(X, y[, eps, ...])`         | Compute `Lasso` path with coordinate descent
`linear_model.logistic_regression_path(X, y)`       | Compute a `Logistic Regression` model for a list of regularization parameters
`linear_model.orthogonal_mp(X, y[, ...])`           | `Orthogonal Matching Pursuit`
`linear_model.orthogonal_mp_gram(Gram, Xy[, ...])`  | `Gram Orthogonal Matching Pursuit`

先看一下`LogisticRegression`这个函数：

``` python
class sklearn.linear_model.LogisticRegression(
    penalty="l2", dual=False, tol=0.0001, C=1.0, fit_intercept=True, intercept_scaling=1,
    class_weight=None, random_state=None, solver="liblinear", max_iter=100, multi_class="ovr",
    verbose=0, warm_start=False, n_jobs=1)
```

- `penalty`：惩罚项，`str`类型，可选参数为`l1`和`l2`，用于指定惩罚项中使用的规范。`newton-cg`、`sag`和`lbfgs`求解算法只支持`L2`规范。`L1`规范假设的是模型的参数满足拉普拉斯分布，`L2`假设的模型参数满足高斯分布。所谓的规范就是加上对参数的约束，使得模型更不会过拟合(`overfit`)，但是如果要说是不是加了约束就会好，这个没有人能回答，只能说在加约束的情况下，理论上应该可以获得泛化能力更强的结果。
- `dual`：对偶或原始方法，`bool`类型。对偶方法只用在求解线性多核(`liblinear`)的`L2`惩罚项上。当样本数量大于样本特征的时候，`dual`通常设置为`False`。
- `tol`：停止求解的标准，`float`类型。就是求解到多少的时候停止，认为已经求出最优解。
- `c`：正则化系数λ的倒数，`float`类型，必须是正浮点型数。像`SVM`一样，越小的数值表示越强的正则化。
- `fit_intercept`：是否存在截距或偏差，`bool`类型。
- `intercept_scaling`：`float`类型。仅在正则化项为`liblinear`且`fit_intercept`设置为`True`时有用。
- `class_weight`：用于标示分类模型中各种类型的权重，可以是一个字典或者`balanced`字符串，默认为不输入，也就是不考虑权重。如果选择输入的话，可以选择`balanced`让类库自己计算类型权重，或者自己输入各个类型的权重。举个例子，比如对于`0`和`1`的二元模型，我们可以定义`class_weight = {0:0.9, 1:0.1}`，这样类型`0`的权重为`90%`，而类型`1`的权重为`10%`；如果`class_weight`选择`balanced`，那么类库会根据训练样本量来计算权重。某种类型样本量越多，则权重越低；样本量越少，则权重越高。当`class_weight`为`balanced`时，类权重计算方法为`n_samples / (n_classes * np.bincount(y))`，`n_samples`为样本数，`n_classes`为类别数量，`np.bincount(y)`会输出每个类的样本数，例如`y = [1, 0, 0, 1, 1]`，则`np.bincount(y) = [2, 3]`。

&emsp;&emsp;那么`class_weight`有什么作用呢？在分类模型中，我们经常会遇到两类问题：

1. 误分类的代价很高。比如对合法用户和非法用户进行分类，将非法用户分类为合法用户的代价很高，我们宁愿将合法用户分类为非法用户，这时可以人工再甄别，但是却不愿将非法用户分类为合法用户。这时我们可以适当提高非法用户的权重。
2. 样本是高度失衡。比如我们有合法用户和非法用户的二元样本数据`10000`条，里面合法用户有`9995`条，非法用户只有`5`条，如果我们不考虑权重，则可以将所有的测试集都预测为合法用户，这样预测准确率理论上有`99.95%`，但是却没有任何意义。这时我们可以选择`balanced`，让类库自动提高非法用户样本的权重。提高了某种分类的权重，相比不考虑权重，会有更多的样本分类划分到高权重的类别，从而可以解决上面两类问题。

- `random_state`：随机数种子，`int`类型，可选参数，仅在正则化优化算法为`sag`、`liblinear`时有用。
- `solver`：优化算法选择参数，只有五个可选参数，即`newton-cg`、`lbfgs`、`liblinear`、`sag`和`saga`，默认为`liblinear`。`solver`参数决定了我们对逻辑回归损失函数的优化方法，有四种算法可以选择，分别是：

1. `liblinear`：使用了开源的`liblinear`库实现，内部使用了坐标轴下降法来迭代优化损失函数。
2. `lbfgs`：拟牛顿法的一种，利用损失函数二阶导数矩阵(即海森矩阵)来迭代优化损失函数。
3. `newton-cg`：也是牛顿法家族的一种，利用损失函数二阶导数矩阵(即海森矩阵)来迭代优化损失函数。
4. `sag`：即随机平均梯度下降，是梯度下降法的变种，和普通梯度下降法的区别是每次迭代仅仅用一部分的样本来计算梯度，适合于样本数据多的时候。
5. `saga`：线性收敛的随机优化算法的的变重。

&emsp;&emsp;`liblinear`适用于小数据集，而`sag`和`saga`适用于大数据集，因为速度更快。
&emsp;&emsp;对于多分类问题，只有`newton-cg`、`sag`、`saga`和`lbfgs`能够处理多项损失，而`liblinear`受限于一对剩余(`OvR`)。意思就是用`liblinear`的时候，如果是多分类问题，得先把一种类别作为一个类别，剩余的所有类别作为另外一个类别。依次类推，最终遍历所有类别，进行分类。
&emsp;&emsp;`newton-cg`、`sag`和`lbfgs`这三种优化算法时都需要损失函数的一阶或者二阶连续导数，因此不能用于没有连续导数的`L1`正则化，只能用于`L2`正则化。而`liblinear`和`saga`通吃`L1`正则化和`L2`正则化。
&emsp;&emsp;同时，`sag`每次仅仅使用了部分样本进行梯度迭代，所以当样本量少的时候不要选择它。而如果样本量非常大，比如大于`10`万，`sag`是第一选择。但是`sag`不能用于`L1`正则化，所以当你有大量的样本又需要`L1`正则化的话，就要自己做取舍了。要么通过对样本采样来降低样本量，要么回到`L2`正则化。
&emsp;&emsp;从上面的描述，大家可能觉得既然`newton-cg`、`lbfgs`和`sag`这么多限制，如果不是大样本，我们选择`liblinear`不就行了吗？这是错误的，因为`liblinear`也有自己的弱点！我们知道，逻辑回归有二元逻辑回归和多元逻辑回归。对于多元逻辑回归常见的有`one-vs-rest`(`OvR`)和`many-vs-many`(`MvM`)两种，而`MvM`一般比`OvR`分类相对准确一些。郁闷的是`liblinear`只支持`OvR`却不支持`MvM`，这样如果我们需要相对精确的多元逻辑回归时，就不能选择`liblinear`了，即不能使用`L1`正则化。

- `max_iter`：算法收敛最大迭代次数，`int`类型。仅在正则化优化算法为`newton-cg`、`sag`和`lbfgs`才有用，算法收敛的最大迭代次数。
- `multi_class`：分类方式选择参数，`str`类型，可选参数为`ovr`和`multinomial`。`ovr`即`one-vs-rest`，而`multinomial`即`many-vs-many`。如果是二元逻辑回归，`ovr`和`multinomial`并没有任何区别，区别主要在多元逻辑回归上。

&emsp;&emsp;`OvR`和`MvM`有什么不同？`OvR`的思想很简单，无论你是多少元逻辑回归，我们都可以看做二元逻辑回归。具体做法是，对于第`K`类的分类决策，我们把所有第`K`类的样本作为正例，除了第`K`类样本以外的所有样本都作为负例，然后在上面做二元逻辑回归，得到第`K`类的分类模型。其他类的分类模型获得以此类推。而`MvM`则相对复杂，这里举`MvM`的特例`one-vs-one`(`OvO`)作讲解。如果模型有`T`类，我们每次在所有的`T`类样本里面选择两类样本出来，不妨记为`T1`类和`T2`类，把所有的输出为`T1`和`T2`的样本放在一起，把`T1`作为正例，`T2`作为负例，进行二元逻辑回归，得到模型参数，一共需要`T(T - 1) / 2`次分类。可以看出`OvR`相对简单，但分类效果相对略差(这里指大多数样本分布情况，某些样本分布下`OvR`可能更好)；`MvM`分类相对精确，但是分类速度没有`OvR`快。如果选择了`ovr`，则`4`种损失函数的优化方法`liblinear`、`newton-cg`、`lbfgs`和`sag`都可以选择。但是如果选择了`multinomial`，则只能选择`newton-cg`、`lbfgs`和`sag`了。

- `verbose`：日志冗长度，`int`类型，就是不输出训练过程；`1`的时候偶尔输出结果；如果大于`1`，对于每个子模型都输出。
- `warm_start`：热启动参数，`bool`类型。如果为`True`，则下一次训练是以追加树的形式进行(重新使用上一次的调用作为初始化)。
- `n_jobs`：并行数，`int`类型。`1`的时候，用`CPU`的一个内核运行程序；`2`的时候，用`CPU`的`2`个内核运行程序；`-1`的时候，用所有`CPU`的内核运行程序。

&emsp;&emsp;`LogisticRegression`也提供了一些方法：

方法                           | 说明
-------------------------------|-------
`decision_function(X)`         | Predict confidence scores for samples
`densify()`                    | Convert coefficient matrix to dense array format
`fit(X, y[, sample_weight])`   | Fit the model according to the given training data
`get_params([deep])`           | Get parameters for this estimator
`predict(X)`                   | Predict class labels for samples in `X`
`predict_log_proba(X)`         | Log of probability estimates
`predict_proba(X)`             | Probability estimates
`score(X, y[, sample_weight])` | Returns the mean accuracy on the given test data and labels
`set_params(**params)`         | Set the parameters of this estimator
`sparsify()`                   | Convert coefficient matrix to sparse format

&emsp;&emsp;针对从疝气病症状预测病马的死亡率的案例，使用`sklearn`的解决方法如下：

``` python
from sklearn.linear_model import LogisticRegression
​
""" 使用Sklearn构建Logistic回归分类器 """
def colicSklearn():
    frTrain = open('horseColicTraining.txt')  # 打开训练集
    frTest = open('horseColicTest.txt')  # 打开测试集
    trainingSet = []
    trainingLabels = []
    testSet = []
    testLabels = []
    for line in frTrain.readlines():
        currLine = line.strip().split('\t')
        lineArr = []
        for i in range(len(currLine) - 1):
            lineArr.append(float(currLine[i]))
        trainingSet.append(lineArr)
        trainingLabels.append(float(currLine[-1]))
    for line in frTest.readlines():
        currLine = line.strip().split('\t')
        lineArr = []
        for i in range(len(currLine) - 1):
            lineArr.append(float(currLine[i]))
        testSet.append(lineArr)
        testLabels.append(float(currLine[-1]))
    classifier = LogisticRegression(solver='liblinear', max_iter=10).fit(trainingSet, trainingLabels)
    test_accurcy = classifier.score(testSet, testLabels) * 100
    print('正确率:%f%%' % test_accurcy)  # 正确率:73.134328%
​
if __name__ == '__main__':
    colicSklearn()
```