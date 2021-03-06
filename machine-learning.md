# 机器学习读书笔记

## 分类

### kNN

找到离判定点最近的N个样本点，以这N个点里面最多的类别做为类别。

样本最好归一化，避免不同特征对于距离的影响。不同特征在计算距离时可加权。

### 决策树

树每次只根据某一个特征分叉，最好的特征在树根，依次往下。

选取最好的特征的方法，是看根据这一特征划分后，整个系统的熵是否减少的最多。

### 朴素贝叶斯

判断标准：若p(ci|W)最大，那么向量W就属于类别ci

根据贝叶斯法则：`p(ci|W) = p(W|ci) * p(ci) / p(W)`

如果W的特征都是独立的，就是朴素贝叶斯：`p(ci|W) = p(w0|ci) * ... * p(wN|ci) * p(ci) / p(W)`

p(W)对于每个p(ci|W)都一样，所以只要针对i，计算每个`p(w0|ci) * ... * p(wN|ci) * p(ci)`即可。

### logistic回归

对于向量X，计算回归系数向量W，得到`z = W.T * X`，将z送入sigmoid函数判定类别。

可以使用梯度上升算法找到最佳回归系数。训练过程：先随便生成一个回归系数，然后计算预测类别与真实类别的误差向量，将误差向量加权加到当前的回归系数上，如此迭代。

梯度上升算法需要遍历所有数据集，可使用随机梯度上升算法，每次只更新一个数据点，这样就可以在线训练。为了避免新的数据点太过异常导致已经稳定的回归系数波动较大，可以降低新的数据点的权重。

数据集中的特征如果有缺失值，可补0或补均值。

### SVM（支持向量机）

支持向量：离分隔超平面最近的那些点。目标就是，先找到支持向量，然后将支持向量的间隔最大化（也就是分隔超平面）。

使用SMO算法（序列最小优化），在训练集上迭代，找到支持向量和alpha。

对于非线性的分类，可使用核函数映射原始数据，然后再用SMO。核函数不通用。

### 其他

元算法：

* bagging：将一个数据集构造成N个数据集，然后训练得到N个分类器。使用N个分类器投票决定预测值。
* boosting：训练得到N个分类器，根据分类器的错误率进行加权得到一个更优的分类器。

AdaBoost：使用若干弱分类器构造一个强分类器。将N个若分类器加权，根据误差迭代更新权重。

非均衡分类概念：

* 正确率 = TP/(TP+FP)
* 召回率 = TP/(TP+FN)

ROC曲线：横坐标假阳率，纵坐标真阳率

* 假阳率 = FP/(FP+TN)
* 真阳率 = TP/(TP+FN)

## 回归

### 线性回归

对于一个数据，向量X1，回归系数向量W，预测值`y1 = X1.T * W`

对于一组数据，矩阵X，那么预测向量`Y = X.T * W`

可解出`W = (X.T * X)^-1 * X.T * Y`

局部加权线性回归：`W = (X.T * R * X)^-1 * X.T * Y`，其中R表示数据点的相关性系数矩阵，可用核函数生成。选取不同的R可以得到非线性的预测效果。

使用岭回归，减少不必要的回归系数：`W = (X.T * X + lambda * I)^-1 * X.T * Y`

其他缩减系数的方法：

* lasso
* 前向逐步回归

### 树回归

CART（分类回归树）：通过先分类再回归，解决非线性数据的回归问题。先对数据集进行分割，生成左子树和右子树。分割的目标是减少总方差。

树剪枝：

* 预剪枝：修改迭代的停止条件topS（容许的误差下降值），减少树枝。
* 后剪枝：生成树之后，尝试合并树枝。如果合并能使误差减少，那么就用合并后的树。

模型树：生成的叶子节点不是常数值而是线性模型。通过预测值的相关性比较，优于回归树，优于线性回归。

## 聚类

### K-means

寻找给定数据集的K个质心。算法：先随机选取K个质心，然后将每个数据点分配到最近的质心，然后根据聚类结果重新计算质心，得到新的K个质心。如此迭代，直到质心位置稳定。缺点是有可能陷入局部最小值。

可以合并使得SSE（误差平方和）增长最小的两个簇，从而减少簇的个数。

二分K-means：选择SSE最大的簇，一分为二。

### Apriori

目的是从数据集（每个数据是一个集合，元素表示item）中，找出频繁项。直接遍历所有item组合，计算量太大不可行。可以以1个item组合开始，排除非频繁项，因为如果某个item是非频繁项，那么包含这个item的组合，肯定也是非频繁项。

### FP-growth

通过构造FP树挖掘频繁项。

## 数据降维

### PCA（主成分分析）

降维技术包括：

* PCA：主成分分析
* FA：因子分析
* ICA：独立成分分析

选取数据差异性最大（方差最大）的方向就是第一主成分。与第一主成分方向正交的成分中，选取差异性最大的方向作为第二主成分。依次类推得到更多的主成分。

数学上，求解协方差矩阵的特征向量和特征值，最大的N个特征值就是最主要的N个成分。

### SVD（奇异值分解）

一个矩阵A，通过奇异值分解，得到的奇异值向量，可忽略其中较小的奇异值。然后将缩短的奇异值向量还原矩阵A，得到的A'约等于A。利用这个原理可用于压缩数据。
