# 分类与回归

&emsp;&emsp;`spark.mllib`提供了多种方法用于用于[二分类](http://en.wikipedia.org/wiki/Binary_classification)、[多分类](http://en.wikipedia.org/wiki/Multiclass_classification)以及[回归分析](http://en.wikipedia.org/wiki/Regression_analysis)。
下表介绍了每种问题类型支持的算法。

| 问题类型       | 支持的方法   |
| ------------- |:-------------:|
| 二分类        | 线性SVMs、逻辑回归、决策树、随机森林、梯度增强树、朴素贝叶斯 |
| 多分类        | 逻辑回归、决策树、随机森林、朴素贝叶斯 |
| 回归          | 线性最小二乘、决策树、随机森林、梯度增强树、保序回归 |

&emsp;&emsp;点击链接，了解具体的算法实现。

* 分类和回归
    * [线性模型](linear/readme.md)
        * [SVMs(支持向量机)](线性模型/支持向量机/lsvm.md)
        * [逻辑回归](linear/logic/logic-regression.md)
        * [线性回归](linear/regression/regression.md)
    * [朴素贝叶斯](nb/nb.md)
    * [决策树](decision/decision-tree.md)
    * [组合树](tree/readme.md)
        * [随机森林](tree/random/random-forests.md)
        * [梯度提升树](tree/gbts/gbts.md)
    * [生存回归](survival/survival-regression.md)
    * [保序回归](isotonic/isotonic-regression.md)


# 线性模型

# 1 数学描述

&emsp;&emsp;许多标准的机器学习算法可以归结为凸优化问题。例如，找到凸函数`f`的一个极小值的任务，这个凸函数依赖于可变向量`w`（在`spark`源码中，一般表示为`weights`）。
形式上，我们可以将其当作一个凸优化问题${min}_{w}f(w)$。它的目标函数可以表示为如下公式 **(1)**：

<div  align="center"><img src="tech/ml/linear/imgs/introduce1.png" width = "280" height = "60" alt="objective function" align="center" /></div><br>

&emsp;&emsp;在上式中，向量`x`表示训练数据集，`y`表示它相应的标签，也是我们想预测的值。如果`L(w;x,y)`可以表示为${w}^{T}x$和`y`的函数，
我们称这个方法为线性的。`spark.mllib`中的几种分类算法和回归算法可以归为这一类。

&emsp;&emsp;目标函数`f`包含两部分：正则化(`regularizer `)，用于控制模型的复杂度；损失函数，用于度量模型的误差。损失函数`L(w;.)`是一个典型的基于`w`的凸函数。固定的正则化参数`gamma`定义了两种目标的权衡（`trade-off`）,
这两个目标分别是最小化损失(训练误差)以及最小化模型复杂度(为了避免过拟合)。

## 1.1 损失函数

&emsp;&emsp;下面介绍`spark.mllib`中提供的几种损失函数以及它们的梯度或子梯度(`sub-gradient`)。

- **hinge loss**

&emsp;&emsp;`hinge`损失的损失函数`L(w;x,y)`以及梯度分别是：

<div  align="center"><img src="tech/ml/linear/imgs/1.1.png" width = "260" height = "30" alt="objective function" align="center" /></div><br>
<div  align="center"><img src="tech/ml/linear/imgs/1.2.png" width = "180" height = "50" alt="objective function" align="center" /></div><br>

- **logistic  loss**

&emsp;&emsp;`logistic`损失的损失函数`L(w;x,y)`以及梯度分别是：

<div  align="center"><img src="tech/ml/linear/imgs/1.3.png" width = "300" height = "30" alt="objective function" align="center" /></div><br>
<div  align="center"><img src="tech/ml/linear/imgs/1.4.png" width = "240" height = "50" alt="objective function" align="center" /></div><br>

- **squared   loss**

&emsp;&emsp;`squared`损失的损失函数`L(w;x,y)`以及梯度分别是：

<div  align="center"><img src="tech/ml/linear/imgs/1.5.png" width = "170" height = "50" alt="objective function" align="center" /></div><br>
<div  align="center"><img src="tech/ml/linear/imgs/1.6.png" width = "120" height = "35" alt="objective function" align="center" /></div><br>

## 1.2 正则化

&emsp;&emsp;正则化的目的是为了简化模型及防止过拟合。`spark.mllib`中提供了下面的正则化方法。

| 问题       | 规则化函数R(w)   | 梯度 |
| ------------- |:-------------:|:-------------:|
| Zero       | 0 | 0 |
| L2         | 如下公式(1) | w |
| L1         | 如下公式(2) | sign(w) |
| elastic net | alpha * L1 +(1-alpha) * L2 | alpha * sign(w) + (1-alpha) * w |

<div  align="center"><img src="tech/ml/linear/imgs/1.7.png" width = "200" height = "40" alt="objective function" align="center" /></div><br>
<div  align="center"><img src="tech/ml/linear/imgs/1.8.png" width = "200" height = "35" alt="objective function" align="center" /></div><br>

&emsp;&emsp;在上面的表格中，`sign(w)`是一个向量，它由`w`中的所有实体的信号量`(+1,-1)`组成。`L2`问题往往比`L1`问题更容易解决，那是因为`L2`是平滑的。然而，`L1`可以使权重矩阵更稀疏，
从而构建更小以及更可判断的模型，模型的可判断性在特征选择中很有用。

# 2 分类

&emsp;&emsp;分类的目的就是将数据切分为不同的类别。最一般的分类类型是二分类，即有两个类别，通常称为正和负。如果类别数超过两个，我们称之为多分类。`spark.ml`提供了两种线性方法用于分类：线性支持向量机以及逻辑回归。
线性支持向量机仅仅支持二分类，逻辑回归既支持二分类也支持多分类。对所有的方法，`spark.ml`支持`L1`和`L2`正则化。分类算法的详细介绍见下面的链接。

* [SVMs(支持向量机)](支持向量机/lsvm.md)
* [逻辑回归](逻辑回归/logic-regression.md)
* [线性回归](回归/regression.md)

# 线性回归

&emsp;&emsp;回归问题的条件或者说前提是

- 1） 收集的数据
- 2） 假设的模型，即一个函数，这个函数里含有未知的参数，通过学习，可以估计出参数。然后利用这个模型去预测/分类新的数据。

## 1 线性回归的概念

&emsp;&emsp;线性回归假设特征和结果都满足线性。即不大于一次方。收集的数据中，每一个分量，就可以看做一个特征数据。每个特征至少对应一个未知的参数。这样就形成了一个线性模型函数，向量表示形式：

<div  align="center"><img src="tech/ml/linear/regression-imgs/1.1.png" width = "120" height = "30" alt="1.1" align="center" /></div>

&emsp;&emsp;这个就是一个组合问题，已知一些数据，如何求里面的未知参数，给出一个最优解。 一个线性矩阵方程，直接求解，很可能无法直接求解。有唯一解的数据集，微乎其微。

&emsp;&emsp;基本上都是解不存在的超定方程组。因此，需要退一步，将参数求解问题，转化为求最小误差问题，求出一个最接近的解，这就是一个松弛求解。

&emsp;&emsp;在回归问题中，线性最小二乘是最普遍的求最小误差的形式。它的损失函数就是二乘损失。如下公式**（1）**所示：

<div  align="center"><img src="tech/ml/linear/regression-imgs/1.2.png" width = "240" height = "50" alt="1.2" align="center" /></div>

&emsp;&emsp;根据使用的正则化类型的不同，回归算法也会有不同。普通最小二乘和线性最小二乘回归不使用正则化方法。`ridge`回归使用`L2`正则化，`lasso`回归使用`L1`正则化。

## 2 线性回归源码分析

### 2.1 实例

```scala
import org.apache.spark.ml.regression.LinearRegression

// 加载数据
val training = spark.read.format("libsvm")
  .load("data/mllib/sample_linear_regression_data.txt")

val lr = new LinearRegression()
  .setMaxIter(10)
  .setRegParam(0.3)
  .setElasticNetParam(0.8)

// 训练模型
val lrModel = lr.fit(training)

// 打印线性回归的系数和截距
println(s"Coefficients: ${lrModel.coefficients} Intercept: ${lrModel.intercept}")

// 打印统计信息
val trainingSummary = lrModel.summary
println(s"numIterations: ${trainingSummary.totalIterations}")
println(s"objectiveHistory: [${trainingSummary.objectiveHistory.mkString(",")}]")
trainingSummary.residuals.show()
println(s"RMSE: ${trainingSummary.rootMeanSquaredError}")
println(s"r2: ${trainingSummary.r2}")
```

### 2.2 代码实现

#### 2.2.1 参数配置

&emsp;&emsp;根据上面的例子，我们先看看线性回归可以配置的参数。

```scala
// 正则化参数，默认为0，对应于优化算法中的lambda
def setRegParam(value: Double): this.type = set(regParam, value)
setDefault(regParam -> 0.0)

// 是否使用截距，默认使用
def setFitIntercept(value: Boolean): this.type = set(fitIntercept, value)
setDefault(fitIntercept -> true)

// 在训练模型前，是否对训练特征进行标准化。默认使用。
// 模型的相关系数总是会返回原来的空间（不是标准化后的标准空间），所以这个过程对用户透明
def setStandardization(value: Boolean): this.type = set(standardization, value)
setDefault(standardization -> true)

// ElasticNet混合参数
// 当改值为0时，使用L2惩罚；当该值为1时，使用L1惩罚；当值在(0,1)之间时，使用L1惩罚和L2惩罚的组合
def setElasticNetParam(value: Double): this.type = set(elasticNetParam, value)
setDefault(elasticNetParam -> 0.0)

// 最大迭代次数，默认是100
def setMaxIter(value: Int): this.type = set(maxIter, value)
setDefault(maxIter -> 100)

// 收敛阈值
def setTol(value: Double): this.type = set(tol, value)
setDefault(tol -> 1E-6)

// 样本权重列的列名。默认不设置。当不设置时，样本权重为1
def setWeightCol(value: String): this.type = set(weightCol, value)

// 最优化求解方法。实际有l-bfgs和带权最小二乘两种求解方法。
// 当特征列数量超过4096时，默认使用l-bfgs求解，否则使用带权最小二乘求解。
def setSolver(value: String): this.type = {
    require(Set("auto", "l-bfgs", "normal").contains(value),
      s"Solver $value was not supported. Supported options: auto, l-bfgs, normal")
    set(solver, value)
  }
setDefault(solver -> "auto")

// 设置treeAggregate的深度。默认情况下深度为2
// 当特征维度较大或者分区较多时，可以调大该深度
def setAggregationDepth(value: Int): this.type = set(aggregationDepth, value)
setDefault(aggregationDepth -> 2)
```

#### 2.2.2 训练模型

&emsp;&emsp;`train`方法训练模型并返回`LinearRegressionModel`。方法的开始是处理数据集，生成需要的`RDD`。

```scala
// Extract the number of features before deciding optimization solver.
val numFeatures = dataset.select(col($(featuresCol))).first().getAs[Vector](0).size
val w = if (!isDefined(weightCol) || $(weightCol).isEmpty) lit(1.0) else col($(weightCol))

val instances: RDD[Instance] = dataset.select(
    col($(labelCol)), w, col($(featuresCol))).rdd.map {
    case Row(label: Double, weight: Double, features: Vector) =>
        Instance(label, weight, features)  // 标签，权重，特征向量
}
```

##### 2.2.2.1 带权最小二乘

&emsp;&emsp;当样本的特征维度小于4096并且`solver`为`auto`或者`solver`为`normal`时，用`WeightedLeastSquares`求解，这是因为`WeightedLeastSquares`只需要处理一次数据，
求解效率更高。`WeightedLeastSquares`的介绍见[带权最小二乘](../../../最优化算法/WeightsLeastSquares.md)。

```scala
if (($(solver) == "auto" &&
    numFeatures <= WeightedLeastSquares.MAX_NUM_FEATURES) || $(solver) == "normal") {

    val optimizer = new WeightedLeastSquares($(fitIntercept), $(regParam),
        elasticNetParam = $(elasticNetParam), $(standardization), true,
        solverType = WeightedLeastSquares.Auto, maxIter = $(maxIter), tol = $(tol))
    val model = optimizer.fit(instances)
    // When it is trained by WeightedLeastSquares, training summary does not
    // attach returned model.
    val lrModel = copyValues(new LinearRegressionModel(uid, model.coefficients, model.intercept))
    val (summaryModel, predictionColName) = lrModel.findSummaryModelAndPredictionCol()
    val trainingSummary = new LinearRegressionTrainingSummary(
        summaryModel.transform(dataset),
        predictionColName,
        $(labelCol),
        $(featuresCol),
        summaryModel,
        model.diagInvAtWA.toArray,
        model.objectiveHistory)

    return lrModel.setSummary(Some(trainingSummary))
}
```

##### 2.2.2.2 拟牛顿法

- <b>1 统计样本指标</b>

&emsp;&emsp;当样本的特征维度大于4096并且`solver`为`auto`或者`solver`为`l-bfgs`时，使用拟牛顿法求解最优解。使用拟牛顿法求解之前我们
需要先统计特征和标签的相关信息。

```scala
val (featuresSummarizer, ySummarizer) = {
      val seqOp = (c: (MultivariateOnlineSummarizer, MultivariateOnlineSummarizer),
        instance: Instance) =>
          (c._1.add(instance.features, instance.weight),
            c._2.add(Vectors.dense(instance.label), instance.weight))

      val combOp = (c1: (MultivariateOnlineSummarizer, MultivariateOnlineSummarizer),
        c2: (MultivariateOnlineSummarizer, MultivariateOnlineSummarizer)) =>
          (c1._1.merge(c2._1), c1._2.merge(c2._2))

      instances.treeAggregate(
        new MultivariateOnlineSummarizer, new MultivariateOnlineSummarizer
      )(seqOp, combOp, $(aggregationDepth))
}
```

&emsp;&emsp;这里`MultivariateOnlineSummarizer`继承自`MultivariateStatisticalSummary`，它使用在线（`online`）的方式统计样本的均值、方差、最小值、最大值等指标。
具体的实现见`MultivariateOnlineSummarizer`。统计好指标之后，根据指标的不同选择不同的处理方式。

&emsp;&emsp; 如果标签的方差为0，并且不管我们是否选择使用偏置，系数均为0，此时并不需要训练模型。

```scala
 val coefficients = Vectors.sparse(numFeatures, Seq())  // 系数为空
 val intercept = yMean
 val model = copyValues(new LinearRegressionModel(uid, coefficients, intercept))
```

&emsp;&emsp;获取标签方差，特征均值、特征方差以及正则化项。

```scala
 // if y is constant (rawYStd is zero), then y cannot be scaled. In this case
 // setting yStd=abs(yMean) ensures that y is not scaled anymore in l-bfgs algorithm.
 val yStd = if (rawYStd > 0) rawYStd else math.abs(yMean)
 val featuresMean = featuresSummarizer.mean.toArray
 val featuresStd = featuresSummarizer.variance.toArray.map(math.sqrt)
 val bcFeaturesMean = instances.context.broadcast(featuresMean)
 val bcFeaturesStd = instances.context.broadcast(featuresStd)

 val effectiveRegParam = $(regParam) / yStd
 val effectiveL1RegParam = $(elasticNetParam) * effectiveRegParam
 val effectiveL2RegParam = (1.0 - $(elasticNetParam)) * effectiveRegParam
```

- <b>2 定义损失函数</b>

```scala
val costFun = new LeastSquaresCostFun(instances, yStd, yMean, $(fitIntercept),
      $(standardization), bcFeaturesStd, bcFeaturesMean, effectiveL2RegParam, $(aggregationDepth))
```

&emsp;&emsp;损失函数`LeastSquaresCostFun`继承自`DiffFunction[T]`，用于表示最小二乘损失。它返回一个点L2正则化后的损失和梯度。
它使用方法`def calculate(coefficients: BDV[Double]): (Double, BDV[Double])`计算损失和梯度。这里`coefficients`表示一个特定的点。

```scala
override def calculate(coefficients: BDV[Double]): (Double, BDV[Double]) = {
    val coeffs = Vectors.fromBreeze(coefficients)
    val bcCoeffs = instances.context.broadcast(coeffs)
    val localFeaturesStd = bcFeaturesStd.value

    val leastSquaresAggregator = {
      val seqOp = (c: LeastSquaresAggregator, instance: Instance) => c.add(instance)
      val combOp = (c1: LeastSquaresAggregator, c2: LeastSquaresAggregator) => c1.merge(c2)

      instances.treeAggregate(
        new LeastSquaresAggregator(bcCoeffs, labelStd, labelMean, fitIntercept, bcFeaturesStd,
          bcFeaturesMean))(seqOp, combOp, aggregationDepth)
    }

    val totalGradientArray = leastSquaresAggregator.gradient.toArray //梯度
    bcCoeffs.destroy(blocking = false)

    val regVal = if (effectiveL2regParam == 0.0) {
      0.0
    } else {
      var sum = 0.0
      coeffs.foreachActive { (index, value) =>
        // 下面的代码计算正则化项的损失和梯度，并将梯度添加到totalGradientArray中
        sum += {
          if (standardization) {
            totalGradientArray(index) += effectiveL2regParam * value
            value * value
          } else {
            if (localFeaturesStd(index) != 0.0) {
              // 如果`standardization`为false，我们仍然标准化数据加快收敛速度。获得的结果，我们需要执行反标准化
              // ，来得到正确的目标函数
              val temp = value / (localFeaturesStd(index) * localFeaturesStd(index))
              totalGradientArray(index) += effectiveL2regParam * temp
              value * temp
            } else {
              0.0
            }
          }
        }
      }
      0.5 * effectiveL2regParam * sum
    }

    (leastSquaresAggregator.loss + regVal, new BDV(totalGradientArray))
  }
```

&emsp;&emsp;这里`LeastSquaresAggregator`用来计算最小二乘损失函数的梯度和损失。为了在优化过程中提高收敛速度，防止大方差
的特征在训练时产生过大的影响，将特征缩放到单元方差并且减去均值，可以减少条件数。当使用截距进行训练时，处在缩放后空间的目标函数
如下：

<blockquote>
$$
\begin{align}
L &= 1/2N ||\sum_i w_i(x_i - \bar{x_i}) / \hat{x_i} - (y - \bar{y}) / \hat{y}||^2
\end{align}
$$


</blockquote>

&emsp;&emsp;在这个公式中，$\bar{x_i}$是$x_i$的均值，$\hat{x_i}$是$x_i$的标准差，$\bar{y}$是标签的均值，$\hat{y}$ 是标签的标准差。

&emsp;&emsp;如果不使用截距，我们可以使用同样的公式。不同的是$\bar{y}$和$\bar{x_i}$分别用0代替。这个公式可以重写为如下的形式。

<blockquote>
$$
\begin{align}
L &= 1/2N ||\sum_i (w_i/\hat{x_i})x_i - \sum_i (w_i/\hat{x_i})\bar{x_i} - y / \hat{y} + \bar{y} / \hat{y}||^2  \\
    &= 1/2N ||\sum_i w_i^\prime x_i - y / \hat{y} + offset||^2 = 1/2N diff^2
\end{align}
$$
</blockquote>


&emsp;&emsp;在这个公式中，$w_i^\prime$是有效的相关系数，通过$w_i/\hat{x_i}$计算。`offset`是$- \sum_i (w_i/\hat{x_i})\bar{x_i} + \bar{y} / \hat{y}$，
而`diff`是$\sum_i w_i^\prime x_i - y / \hat{y} + offset$。

&emsp;&emsp;注意，相关系数和`offset`不依赖于训练数据集，所以它们可以提前计算。

&emsp;&emsp;现在，目标函数的一阶导数如下所示：

<blockquote>
$$
\begin{align}
\frac{\partial L}{\partial w_i} &= diff/N (x_i - \bar{x_i}) / \hat{x_i}
\end{align}
$$
</blockquote>


&emsp;&emsp;然而，$(x_i - \bar{x_i})$是一个密集的计算，当训练数据集是稀疏的格式时，这不是一个理想的公式。通过添加一个稠密项 $\bar{x_i} / \hat{x_i}$到
公式的末尾可以解决这个问题。目标函数的一阶导数如下所示：

<blockquote>
$$
\begin{align}
\frac{\partial L}{\partial w_i} &=1/N \sum_j diff_j (x_{ij} - \bar{x_i}) / \hat{x_i} \\
         &= 1/N ((\sum_j diff_j x_{ij} / \hat{x_i}) - diffSum \bar{x_i} / \hat{x_i}) \\
         &= 1/N ((\sum_j diff_j x_{ij} / \hat{x_i}) + correction_i)
\end{align}
$$
</blockquote>


&emsp;&emsp;这里，$correction_i = - diffSum \bar{x_i} / \hat{x_i}$。通过一个简单的数学推导，我们就可以知道`diffSum`实际上为0。

<blockquote>
    $$
    \begin{align}
       diffSum &= \sum_j (\sum_i w_i(x_{ij} - \bar{x_i})
                    / \hat{x_i} - (y_j - \bar{y}) / \hat{y}) \\
         &= N * (\sum_i w_i(\bar{x_i} - \bar{x_i}) / \hat{x_i} - (\bar{y} - \bar{y}) / \hat{y}) \\
         &= 0
    \end{align}
    $$
</blockquote>


&emsp;&emsp;所以，目标函数的一阶导数仅仅依赖于训练数据集，我们可以简单的通过分布式的方式来计算，并且对稀疏格式也很友好。

<blockquote>
    $$
    \begin{align}
    \frac{\partial L}{\partial w_i} &= 1/N ((\sum_j diff_j x_{ij} / \hat{x_i})
    \end{align}
    $$
</blockquote>


&emsp;&emsp;我们首先看有效系数$w_i/\hat{x_i}$和`offset`的实现。

```scala
@transient private lazy val effectiveCoefAndOffset = {
    val coefficientsArray = bcCoefficients.value.toArray.clone() //系数，表示公式中的w
    val featuresMean = bcFeaturesMean.value
    var sum = 0.0
    var i = 0
    val len = coefficientsArray.length
    while (i < len) {
      if (featuresStd(i) != 0.0) {
        coefficientsArray(i) /=  featuresStd(i)
        sum += coefficientsArray(i) * featuresMean(i)
      } else {
        coefficientsArray(i) = 0.0
      }
      i += 1
    }
    val offset = if (fitIntercept) labelMean / labelStd - sum else 0.0
    (Vectors.dense(coefficientsArray), offset)
  }
```

&emsp;&emsp;我们再来看看`add`方法和`merge`方法的实现。当添加一个样本后，需要更新相应的损失值和梯度值。

```scala
def add(instance: Instance): this.type = {
  instance match { case Instance(label, weight, features) =>
  if (weight == 0.0) return this
  // 计算diff
  val diff = dot(features, effectiveCoefficientsVector) - label / labelStd + offset
  if (diff != 0) {
      val localGradientSumArray = gradientSumArray
      val localFeaturesStd = featuresStd
      features.foreachActive { (index, value) =>
         if (localFeaturesStd(index) != 0.0 && value != 0.0) {
           localGradientSumArray(index) += weight * diff * value / localFeaturesStd(index) // 见公式(11)
         }
      }
      lossSum += weight * diff * diff / 2.0   //见公式(3)
  }
  totalCnt += 1
  weightSum += weight
  this
}

def merge(other: LeastSquaresAggregator): this.type = {
    if (other.weightSum != 0) {
      totalCnt += other.totalCnt
      weightSum += other.weightSum
      lossSum += other.lossSum

      var i = 0
      val localThisGradientSumArray = this.gradientSumArray
      val localOtherGradientSumArray = other.gradientSumArray
      while (i < dim) {
        localThisGradientSumArray(i) += localOtherGradientSumArray(i)
        i += 1
      }
    }
    this
  }
```

&emsp;&emsp;最后，根据下面的公式分别获取损失和梯度。

```scala
  def loss: Double = {
    lossSum / weightSum
  }

  def gradient: Vector = {
    val result = Vectors.dense(gradientSumArray.clone())
    scal(1.0 / weightSum, result)
    result
  }
```

- <b>3 选择最优化方法</b>

```scala
    val optimizer = if ($(elasticNetParam) == 0.0 || effectiveRegParam == 0.0) {
      new BreezeLBFGS[BDV[Double]]($(maxIter), 10, $(tol))
    } else {
      val standardizationParam = $(standardization)
      def effectiveL1RegFun = (index: Int) => {
        if (standardizationParam) {
          effectiveL1RegParam
        } else {
          // If `standardization` is false, we still standardize the data
          // to improve the rate of convergence; as a result, we have to
          // perform this reverse standardization by penalizing each component
          // differently to get effectively the same objective function when
          // the training dataset is not standardized.
          if (featuresStd(index) != 0.0) effectiveL1RegParam / featuresStd(index) else 0.0
        }
      }
      new BreezeOWLQN[Int, BDV[Double]]($(maxIter), 10, effectiveL1RegFun, $(tol))
    }
```

&emsp;&emsp;如果没有正则化项或者只有L2正则化项，使用`BreezeLBFGS`来处理最优化问题，否则使用`BreezeOWLQN`。`BreezeLBFGS`和`BreezeOWLQN`
的原理在相关章节会做具体介绍。

- <b>4 获取结果，并做相应转换</b>

```scala
val initialCoefficients = Vectors.zeros(numFeatures)
    val states = optimizer.iterations(new CachedDiffFunction(costFun),
      initialCoefficients.asBreeze.toDenseVector)

    val (coefficients, objectiveHistory) = {
      val arrayBuilder = mutable.ArrayBuilder.make[Double]
      var state: optimizer.State = null
      while (states.hasNext) {
        state = states.next()
        arrayBuilder += state.adjustedValue
      }

      // 从标准空间转换到原来的空间
      val rawCoefficients = state.x.toArray.clone()
      var i = 0
      val len = rawCoefficients.length
      while (i < len) {
        rawCoefficients(i) *= { if (featuresStd(i) != 0.0) yStd / featuresStd(i) else 0.0 }
        i += 1
      }

      (Vectors.dense(rawCoefficients).compressed, arrayBuilder.result())
    }

    // 系数收敛之后，intercept的计算可以通过封闭(`closed form`)的形式计算出来，详细的讨论如下：
    // http://stats.stackexchange.com/questions/13617/how-is-the-intercept-computed-in-glmnet
    val intercept = if ($(fitIntercept)) {
      yMean - dot(coefficients, Vectors.dense(featuresMean))
    } else {
      0.0
    }

```

# 广义线性回归

## 1 普通线性模型

&emsp;&emsp;普通线性模型(`ordinary linear model`)可以用下式表示：

$$Y = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + … + \beta_{p-1} x_{p-1} + \epsilon$$

&emsp;&emsp;这里$\beta$是未知参数，$\epsilon$是误差项。普通线性模型主要有以下几点假设：

- 响应变量$Y$和误差项$\epsilon$均服从正太分布。其中$\epsilon \sim N(0,{{\sigma }^{2}})$，$Y\sim N({{\beta }^{T}}x,{{\sigma }^{2}})$。
- 预测量$x_i$和未知参数$\beta_i$均具有非随机性。预测量$x_i$具有非随机性、可测且不存在测量误差；未知参数$\beta_i$被认为是未知但不具随机性的常数。
- 普通线性模型的输出项是随机变量$Y$。普通线性模型主要研究响应变量的期望$E[Y]$。
- 连接方式：在上面三点假设下，对上式两边取数学期望，可得

$$E[Y] = \beta_0 + \beta_1 x_1 + \beta_2 x_2 + … + \beta_{p-1} x_{p-1}$$

&emsp;&emsp;在普通线性模型里，响应变量的均值$E[Y]$与预测量的线性组合$\beta_0 + \beta_1 x_1 + \beta_2 x_2 + … + \beta_{p-1} x_{p-1}$通过恒等式(`identity`)连接。
也可以说是通过$f(x)=x$这个链接函数(`link function`)连接。

## 2 广义线性模型

&emsp;&emsp;广义线性模型(`generalized linear model`)是在普通线性模型的基础上，对上述四点假设进行推广而得出的应用范围更广，更具实用性的回归模型。
主要有两点不同，这两点分别是：

- 响应变量$Y$和误差项$\epsilon$的分布推广至指数分散族(`exponential dispersion family`)。在`spark ml`中，广义线性回归支持的指数分布分别是正态分布、泊松分布、二项分布以及伽玛分布。
- 连接方式：广义线性模型里采用的链接函数(`link function`)理论上可以是任意的，而不再局限于$f(x)=x$。

&emsp;&emsp;这里需要重点说明一下链接函数。链接函数描述了线性预测$X\beta$与分布期望值$E[Y]$的关系：$E[Y] = \mu = g^{-1}(X\beta)$，其中$g$表示链接函数，$\mu$表示均值函数。
一般情况下，高斯分布对应于恒等式，泊松分布对应于自然对数函数等。下面列出了`spark ml`中提供的链接函数以及该链接函数使用的指数分布。

| 连接函数名称     | 链接函数                                  | 均值函数                                        | 对应的指数分布               |
| ---------------- | ----------------------------------------- | ----------------------------------------------- | ---------------------------- |
| identity（恒等） | $\mu = X\beta$                            | $\mu = X\beta$                                  | 高斯分布，泊松分布，伽马分布 |
| inverse（倒数）  | $\mu^{-1} = X\beta$                       | $\mu = (X\beta)^{-1}$                           | 高斯分布，伽马分布           |
| sqrt(均分)       | $\mu^{1/2} = X\beta$                      | $\mu = (X\beta)^{2}$                            | 泊松分布                     |
| log（对数）      | $ln(\mu) = X\beta$                        | $\mu = exp(X\beta)$                             | 高斯分布，泊松分布，伽马分布 |
| logit            | $ln(\frac{\mu }{1-\mu }) = X\beta$        | $\mu = \frac{exp(X\beta)}{1 + exp(1 + X\beta)}$ | 高斯分布，泊松分布，伽马分布 |
| cloglog          | $ln(- ln(1-\mu)) = X\beta$                | $\mu = 1 - exp(- exp(X\beta))$                  | 二次分布                     |
| probit           | 标准高斯分布的inverse cdf，其中p值为$\mu$ | 标准高斯分布的cdf                               | 二次分布                     |

## 3 源码分析

### 3.1 使用实例

```scala
import org.apache.spark.ml.regression.GeneralizedLinearRegression

// Load training data
val dataset = spark.read.format("libsvm")
  .load("data/mllib/sample_linear_regression_data.txt")

val glr = new GeneralizedLinearRegression()
  .setFamily("gaussian")
  .setLink("identity")
  .setMaxIter(10)
  .setRegParam(0.3)

// Fit the model
val model = glr.fit(dataset)

// Print the coefficients and intercept for generalized linear regression model
println(s"Coefficients: ${model.coefficients}")
println(s"Intercept: ${model.intercept}")
```

### 3.2 训练模型

&emsp;&emsp;广义线性回归的训练比较简单。当指数分布是高斯分布，同时链接函数是恒等(`identity`)时，此时的情况就是普通的线性回归。可以利用带权最小二乘求解。

```scala
 val model = if (familyObj == Gaussian && linkObj == Identity) {
      val optimizer = new WeightedLeastSquares($(fitIntercept), $(regParam), elasticNetParam = 0.0,
        standardizeFeatures = true, standardizeLabel = true)
      val wlsModel = optimizer.fit(instances)
      val model = copyValues(
        new GeneralizedLinearRegressionModel(uid, wlsModel.coefficients, wlsModel.intercept)
          .setParent(this))
      val trainingSummary = new GeneralizedLinearRegressionTrainingSummary(dataset, model,
        wlsModel.diagInvAtWA.toArray, 1, getSolver)
      model.setSummary(Some(trainingSummary))
 }
```

&emsp;&emsp;如果是其它的情况，使用迭代再加权最小二乘(`Iteratively reweighted least squares(IRLS)`)求解。

```scala
// Fit Generalized Linear Model by iteratively reweighted least squares (IRLS).
   val initialModel = familyAndLink.initialize(instances, $(fitIntercept), $(regParam))
   val optimizer = new IterativelyReweightedLeastSquares(initialModel,
        familyAndLink.reweightFunc, $(fitIntercept), $(regParam), $(maxIter), $(tol))
   val irlsModel = optimizer.fit(instances)
   val model = copyValues(
     new GeneralizedLinearRegressionModel(uid, irlsModel.coefficients, irlsModel.intercept)
          .setParent(this))
   val trainingSummary = new GeneralizedLinearRegressionTrainingSummary(dataset, model,
        irlsModel.diagInvAtWA.toArray, irlsModel.numIterations, getSolver)
   model.setSummary(Some(trainingSummary))
```

&emsp;&emsp;迭代再加权最小二乘的分析见最优化章节：[迭代再加权最小二乘](../../../最优化算法/IRLS.md)。

### 3.3 链接函数

&emsp;&emsp;根据第二章中表格描述的链接函数和均值函数，我们可以很容易实现链接函数。链接函数和均值函数的值可以用于对样本进行更新，
更新相应的标签值和权重值。

- Identity

```scala
private[regression] object Identity extends Link("identity") {
    override def link(mu: Double): Double = mu  // 链接函数
    override def deriv(mu: Double): Double = 1.0  // 链接函数求导数
    override def unlink(eta: Double): Double = eta  // 均值函数
  }
```

- Logit

```scala
private[regression] object Logit extends Link("logit") {
    override def link(mu: Double): Double = math.log(mu / (1.0 - mu)) // 链接函数
    override def deriv(mu: Double): Double = 1.0 / (mu * (1.0 - mu)) // 链接函数导数
    override def unlink(eta: Double): Double = 1.0 / (1.0 + math.exp(-1.0 * eta)) // 均值函数
  }
```

- Log

```scala
  private[regression] object Log extends Link("log") {
    override def link(mu: Double): Double = math.log(mu) // 链接函数
    override def deriv(mu: Double): Double = 1.0 / mu // 链接函数导数
    override def unlink(eta: Double): Double = math.exp(eta) // 均值函数
  }
```

- Inverse

```scala
  private[regression] object Inverse extends Link("inverse") {
    override def link(mu: Double): Double = 1.0 / mu // 链接函数
    override def deriv(mu: Double): Double = -1.0 * math.pow(mu, -2.0) // 链接函数导数
    override def unlink(eta: Double): Double = 1.0 / eta // 均值函数
  }
```

- Probit

```scala
  private[regression] object Probit extends Link("probit") {
    override def link(mu: Double): Double = dist.Gaussian(0.0, 1.0).icdf(mu) // 链接函数
    override def deriv(mu: Double): Double = {
      1.0 / dist.Gaussian(0.0, 1.0).pdf(dist.Gaussian(0.0, 1.0).icdf(mu)) // 链接函数导数
    }
    override def unlink(eta: Double): Double = dist.Gaussian(0.0, 1.0).cdf(eta) // 均值函数
  }
```

- CLogLog

```scala
  private[regression] object CLogLog extends Link("cloglog") {
    override def link(mu: Double): Double = math.log(-1.0 * math.log(1 - mu)) // 链接函数
    override def deriv(mu: Double): Double = 1.0 / ((mu - 1.0) * math.log(1.0 - mu)) // 链接函数导数
    override def unlink(eta: Double): Double = 1.0 - math.exp(-1.0 * math.exp(eta)) // 均值函数
  }
```

- Sqrt

```scala
  private[regression] object Sqrt extends Link("sqrt") {
    override def link(mu: Double): Double = math.sqrt(mu) // 链接函数
    override def deriv(mu: Double): Double = 1.0 / (2.0 * math.sqrt(mu)) // 链接函数导数
    override def unlink(eta: Double): Double = eta * eta // 均值函数
  }
```

## 参考文献

【1】[从线性模型到广义线性模型](http://cos.name/2011/01/how-does-glm-generalize-lm-assumption/)

【2】[广义线性模型-维基百科](https://zh.wikipedia.org/wiki/%E5%BB%A3%E7%BE%A9%E7%B7%9A%E6%80%A7%E6%A8%A1%E5%9E%8B)

# 逻辑回归

## 1 二元逻辑回归

&emsp;&emsp;回归是一种很容易理解的模型，就相当于`y=f(x)`，表明自变量`x`与因变量`y`的关系。最常见问题如医生治病时的望、闻、问、切，之后判定病人是否生病或生了什么病，
其中的望、闻、问、切就是获取的自变量`x`，即特征数据，判断是否生病就相当于获取因变量`y`，即预测分类。最简单的回归是线性回归，但是线性回归的鲁棒性很差。

&emsp;&emsp;逻辑回归是一种减小预测范围，将预测值限定为`[0,1]`间的一种回归模型，其回归方程与回归曲线如下图所示。逻辑曲线在`z=0`时，十分敏感，在`z>>0`或`z<<0`时，都不敏感。

<div  align="center"><img src="tech/ml/linear/logic-imgs/1.1.png" width = "590" height = "300" alt="1.1" align="center" /></div><br>

&emsp;&emsp;逻辑回归其实是在线性回归的基础上，套用了一个逻辑函数。上图的`g(z)`就是这个逻辑函数(或称为`Sigmoid`函数)。下面左图是一个线性的决策边界，右图是非线性的决策边界。

<div  align="center"><img src="tech/ml/linear/logic-imgs/1.2.png" width = "700" height = "280" alt="1.2" align="center" /></div><br>

&emsp;&emsp;对于线性边界的情况，边界形式可以归纳为如下公式 **(1)**:

<div  align="center"><img src="tech/ml/linear/logic-imgs/1.3.png" width = "300" height = "60" alt="1.3" align="center" /></div><br>

&emsp;&emsp;因此我们可以构造预测函数为如下公式 **(2)**:

<div  align="center"><img src="tech/ml/linear/logic-imgs/1.4.png" width = "250" height = "70" alt="1.4" align="center" /></div><br>

&emsp;&emsp;该预测函数表示分类结果为1时的概率。因此对于输入点`x`，分类结果为类别1和类别0的概率分别为如下公式 **(3)**：

<div  align="center"><img src="tech/ml/linear/logic-imgs/1.5.png" width = "220" height = "55" alt="1.5" align="center" /></div><br>

&emsp;&emsp;对于训练数据集，特征数据`x={x1, x2, … , xm}`和对应的分类数据`y={y1, y2, … , ym}`。构建逻辑回归模型`f`，最典型的构建方法便是应用极大似然估计。对公式 **(3)** 取极大似然函数，可以得到如下的公式 **(4)**:

<div  align="center"><img src="tech/ml/linear/logic-imgs/1.6.png" width = "350" height = "45" alt="1.6" align="center" /></div><br>

&emsp;&emsp;再对公式 **(4)** 取对数，可得到公式 **(5)**：

<div  align="center"><img src="tech/ml/linear/logic-imgs/1.7.png" width = "450" height = "50" alt="1.7" align="center" /></div><br>

&emsp;&emsp;最大似然估计就是求使`l`取最大值时的`theta`。`MLlib`中提供了两种方法来求这个参数，分别是[梯度下降法](../../../最优化算法/梯度下降/gradient-descent.md)和[L-BFGS](../../../最优化算法/L-BFGS/lbfgs.md)。

## 2 多元逻辑回归

&emsp;&emsp;二元逻辑回归可以一般化为[多元逻辑回归](http://en.wikipedia.org/wiki/Multinomial_logistic_regression)用来训练和预测多分类问题。对于多分类问题，算法将会训练出一个多元逻辑回归模型，
它包含`K-1`个二元回归模型。给定一个数据点，`K-1`个模型都会运行，概率最大的类别将会被选为预测类别。

&emsp;&emsp;对于输入点`x`，分类结果为各类别的概率分别为如下公式 **(6)** ，其中`k`表示类别个数。

<div  align="center"><img src="tech/ml/linear/logic-imgs/2.1.png" width = "330" height = "175" alt="2.1" align="center" /></div><br>

&emsp;&emsp;对于`k`类的多分类问题，模型的权重`w = (w_1, w_2, ..., w_{K-1})`是一个矩阵，如果添加截距，矩阵的维度为`(K-1) * (N+1)`，否则为`(K-1) * N`。单个样本的目标函数的损失函数可以写成如下公式 **(7)** 的形式。

<div  align="center"><img src="tech/ml/linear/logic-imgs/2.2.png" width = "720" height = "170" alt="2.2" align="center" /></div><br>

&emsp;&emsp;对损失函数求一阶导数，我们可以得到下面的公式 **(8)**:

<div  align="center"><img src="tech/ml/linear/logic-imgs/2.3.png" width = "570" height = "140" alt="2.3" align="center" /></div><br>

&emsp;&emsp;根据上面的公式，如果某些`margin`的值大于709.78，`multiplier`以及逻辑函数的计算会出现算术溢出(`arithmetic overflow`)的情况。这个问题发生在有离群点远离超平面的情况下。
幸运的是，当`max(margins) = maxMargin > 0`时，损失函数可以重写为如下公式 **(9)** 的形式。

<div  align="center"><img src="tech/ml/linear/logic-imgs/2.4.png" width = "690" height = "190" alt="2.4" align="center" /></div><br>

&emsp;&emsp;同理，`multiplier`也可以重写为如下公式 **(10)** 的形式。

<div  align="center"><img src="tech/ml/linear/logic-imgs/2.5.png" width = "500" height = "160" alt="2.5" align="center" /></div><br>

## 3 逻辑回归的优缺点

- 优点：计算代价低，速度快，容易理解和实现。
- 缺点：容易欠拟合，分类和回归的精度不高。

## 4 实例

&emsp;&emsp;下面的例子展示了如何使用逻辑回归。

```scala
import org.apache.spark.SparkContext
import org.apache.spark.mllib.classification.{LogisticRegressionWithLBFGS, LogisticRegressionModel}
import org.apache.spark.mllib.evaluation.MulticlassMetrics
import org.apache.spark.mllib.regression.LabeledPoint
import org.apache.spark.mllib.linalg.Vectors
import org.apache.spark.mllib.util.MLUtils
// 加载训练数据
val data = MLUtils.loadLibSVMFile(sc, "data/mllib/sample_libsvm_data.txt")
// 切分数据，training (60%) and test (40%).
val splits = data.randomSplit(Array(0.6, 0.4), seed = 11L)
val training = splits(0).cache()
val test = splits(1)
// 训练模型
val model = new LogisticRegressionWithLBFGS()
  .setNumClasses(10)
  .run(training)
// Compute raw scores on the test set.
val predictionAndLabels = test.map { case LabeledPoint(label, features) =>
  val prediction = model.predict(features)
  (prediction, label)
}
// Get evaluation metrics.
val metrics = new MulticlassMetrics(predictionAndLabels)
val precision = metrics.precision
println("Precision = " + precision)
// 保存和加载模型
model.save(sc, "myModelPath")
val sameModel = LogisticRegressionModel.load(sc, "myModelPath")
```

## 5 源码分析

### 5.1 训练模型

&emsp;&emsp;如上所述，在`MLlib`中，分别使用了梯度下降法和`L-BFGS`实现逻辑回归参数的计算。这两个算法的实现我们会在最优化章节介绍，这里我们介绍公共的部分。

&emsp;&emsp;`LogisticRegressionWithLBFGS`和`LogisticRegressionWithSGD`的入口函数均是`GeneralizedLinearAlgorithm.run`，下面详细分析该方法。

```scala
def run(input: RDD[LabeledPoint]): M = {
    if (numFeatures < 0) {
      //计算特征数
      numFeatures = input.map(_.features.size).first()
    }
    val initialWeights = {
          if (numOfLinearPredictor == 1) {
            Vectors.zeros(numFeatures)
          } else if (addIntercept) {
            Vectors.zeros((numFeatures + 1) * numOfLinearPredictor)
          } else {
            Vectors.zeros(numFeatures * numOfLinearPredictor)
          }
    }
    run(input, initialWeights)
}
```

&emsp;&emsp;上面的代码初始化权重向量，向量的值均初始化为0。需要注意的是，`addIntercept`表示是否添加截距(`Intercept`，指函数图形与坐标的交点到原点的距离)，默认是不添加的。`numOfLinearPredictor`表示二元逻辑回归模型的个数。
我们重点看`run(input, initialWeights)`的实现。它的实现分四步。

#### 5.1.1 根据提供的参数缩放特征并添加截距

```scala
val scaler = if (useFeatureScaling) {
      new StandardScaler(withStd = true, withMean = false).fit(input.map(_.features))
    } else {
      null
    }
val data =
      if (addIntercept) {
        if (useFeatureScaling) {
          input.map(lp => (lp.label, appendBias(scaler.transform(lp.features)))).cache()
        } else {
          input.map(lp => (lp.label, appendBias(lp.features))).cache()
        }
      } else {
        if (useFeatureScaling) {
          input.map(lp => (lp.label, scaler.transform(lp.features))).cache()
        } else {
          input.map(lp => (lp.label, lp.features))
        }
      }
val initialWeightsWithIntercept = if (addIntercept && numOfLinearPredictor == 1) {
      appendBias(initialWeights)
    } else {
      /** If `numOfLinearPredictor > 1`, initialWeights already contains intercepts. */
      initialWeights
    }
```

&emsp;&emsp;在最优化过程中，收敛速度依赖于训练数据集的条件数(`condition number`)，缩放变量经常可以启发式地减少这些条件数，提高收敛速度。不减少条件数，一些混合有不同范围列的数据集可能不能收敛。
在这里使用`StandardScaler`将数据集的特征进行缩放。详细信息请看[StandardScaler](../../../特征抽取和转换/StandardScaler.md)。`appendBias`方法很简单，就是在每个向量后面加一个值为1的项。

```scala
def appendBias(vector: Vector): Vector = {
    vector match {
      case dv: DenseVector =>
        val inputValues = dv.values
        val inputLength = inputValues.length
        val outputValues = Array.ofDim[Double](inputLength + 1)
        System.arraycopy(inputValues, 0, outputValues, 0, inputLength)
        outputValues(inputLength) = 1.0
        Vectors.dense(outputValues)
      case sv: SparseVector =>
        val inputValues = sv.values
        val inputIndices = sv.indices
        val inputValuesLength = inputValues.length
        val dim = sv.size
        val outputValues = Array.ofDim[Double](inputValuesLength + 1)
        val outputIndices = Array.ofDim[Int](inputValuesLength + 1)
        System.arraycopy(inputValues, 0, outputValues, 0, inputValuesLength)
        System.arraycopy(inputIndices, 0, outputIndices, 0, inputValuesLength)
        outputValues(inputValuesLength) = 1.0
        outputIndices(inputValuesLength) = dim
        Vectors.sparse(dim + 1, outputIndices, outputValues)
      case _ => throw new IllegalArgumentException(s"Do not support vector type ${vector.getClass}")
    }
```

#### 5.1.2 使用最优化算法计算最终的权重值

```scala
val weightsWithIntercept = optimizer.optimize(data, initialWeightsWithIntercept)
```

&emsp;&emsp;有梯度下降算法和`L-BFGS`两种算法来计算最终的权重值，查看[梯度下降法](../../../最优化算法/梯度下降/gradient-descent.md)和[L-BFGS](../../../最优化算法/L-BFGS/lbfgs.md)了解详细实现。
这两种算法均使用`Gradient`的实现类计算梯度，使用`Updater`的实现类更新参数。在 `LogisticRegressionWithSGD` 和 `LogisticRegressionWithLBFGS` 中，它们均使用 `LogisticGradient` 实现类计算梯度，使用 `SquaredL2Updater` 实现类更新参数。

```scala
//在GradientDescent中
private val gradient = new LogisticGradient()
private val updater = new SquaredL2Updater()
override val optimizer = new GradientDescent(gradient, updater)
    .setStepSize(stepSize)
    .setNumIterations(numIterations)
    .setRegParam(regParam)
    .setMiniBatchFraction(miniBatchFraction)
//在LBFGS中
override val optimizer = new LBFGS(new LogisticGradient, new SquaredL2Updater)
```

&emsp;&emsp;下面将详细介绍`LogisticGradient`的实现和`SquaredL2Updater`的实现。

- LogisticGradient

&emsp;&emsp;`LogisticGradient`中使用`compute`方法计算梯度。计算分为两种情况，即二元逻辑回归的情况和多元逻辑回归的情况。虽然多元逻辑回归也可以实现二元分类，但是为了效率，`compute`方法仍然实现了一个二元逻辑回归的版本。

```scala
val margin = -1.0 * dot(data, weights)
val multiplier = (1.0 / (1.0 + math.exp(margin))) - label
//y += a * x，即cumGradient += multiplier * data
axpy(multiplier, data, cumGradient)
if (label > 0) {
    // The following is equivalent to log(1 + exp(margin)) but more numerically stable.
    MLUtils.log1pExp(margin)
} else {
    MLUtils.log1pExp(margin) - margin
}
```

&emsp;&emsp;这里的`multiplier`就是上文的公式 **(2)** 。`axpy`方法用于计算梯度，这里表示的意思是`h(x) * x`。下面是多元逻辑回归的实现方法。

```scala
//权重
val weightsArray = weights match {
    case dv: DenseVector => dv.values
    case _ =>
            throw new IllegalArgumentException
}
//梯度
val cumGradientArray = cumGradient match {
    case dv: DenseVector => dv.values
    case _ =>
        throw new IllegalArgumentException
}
// 计算所有类别中最大的margin
var marginY = 0.0
var maxMargin = Double.NegativeInfinity
var maxMarginIndex = 0
val margins = Array.tabulate(numClasses - 1) { i =>
    var margin = 0.0
    data.foreachActive { (index, value) =>
        if (value != 0.0) margin += value * weightsArray((i * dataSize) + index)
    }
    if (i == label.toInt - 1) marginY = margin
    if (margin > maxMargin) {
            maxMargin = margin
            maxMarginIndex = i
    }
    margin
}
//计算sum，保证每个margin都小于0，避免出现算术溢出的情况
val sum = {
     var temp = 0.0
     if (maxMargin > 0) {
         for (i <- 0 until numClasses - 1) {
              margins(i) -= maxMargin
              if (i == maxMarginIndex) {
                temp += math.exp(-maxMargin)
              } else {
                temp += math.exp(margins(i))
              }
         }
     } else {
         for (i <- 0 until numClasses - 1) {
              temp += math.exp(margins(i))
         }
     }
     temp
}
//计算multiplier并计算梯度
for (i <- 0 until numClasses - 1) {
     val multiplier = math.exp(margins(i)) / (sum + 1.0) - {
          if (label != 0.0 && label == i + 1) 1.0 else 0.0
     }
     data.foreachActive { (index, value) =>
         if (value != 0.0) cumGradientArray(i * dataSize + index) += multiplier * value
     }
}
//计算损失函数,
val loss = if (label > 0.0) math.log1p(sum) - marginY else math.log1p(sum)
if (maxMargin > 0) {
     loss + maxMargin
} else {
     loss
}
```

- SquaredL2Updater

```scala
class SquaredL2Updater extends Updater {
  override def compute(
      weightsOld: Vector,
      gradient: Vector,
      stepSize: Double,
      iter: Int,
      regParam: Double): (Vector, Double) = {
    // w' = w - thisIterStepSize * (gradient + regParam * w)
    // w' = (1 - thisIterStepSize * regParam) * w - thisIterStepSize * gradient
    //表示步长，即负梯度方向的大小
    val thisIterStepSize = stepSize / math.sqrt(iter)
    val brzWeights: BV[Double] = weightsOld.toBreeze.toDenseVector
    //正则化，brzWeights每行数据均乘以(1.0 - thisIterStepSize * regParam)
    brzWeights :*= (1.0 - thisIterStepSize * regParam)
    //y += x * a，即brzWeights -= gradient * thisInterStepSize
    brzAxpy(-thisIterStepSize, gradient.toBreeze, brzWeights)
    //正则化||w||_2
    val norm = brzNorm(brzWeights, 2.0)
    (Vectors.fromBreeze(brzWeights), 0.5 * regParam * norm * norm)
  }
}
```

&emsp;&emsp;该函数的实现规则是：

```scala
w' = w - thisIterStepSize * (gradient + regParam * w)
w' = (1 - thisIterStepSize * regParam) * w - thisIterStepSize * gradient
```

&emsp;&emsp;这里`thisIterStepSize`表示参数沿负梯度方向改变的速率，它随着迭代次数的增多而减小。

#### 5.1.3 对最终的权重值进行后处理

```scala
val intercept = if (addIntercept && numOfLinearPredictor == 1) {
      weightsWithIntercept(weightsWithIntercept.size - 1)
    } else {
      0.0
    }
var weights = if (addIntercept && numOfLinearPredictor == 1) {
      Vectors.dense(weightsWithIntercept.toArray.slice(0, weightsWithIntercept.size - 1))
    } else {
      weightsWithIntercept
    }
```

&emsp;&emsp;该段代码获得了截距（`intercept`）以及最终的权重值。由于截距（`intercept`）和权重是在收缩的空间进行训练的，所以我们需要再把它们转换到原始的空间。数学知识告诉我们，如果我们仅仅执行标准化而没有减去均值，即`withStd = true, withMean = false`，
那么截距（`intercept`）的值并不会发送改变。所以下面的代码仅仅处理权重向量。

```scala
if (useFeatureScaling) {
      if (numOfLinearPredictor == 1) {
        weights = scaler.transform(weights)
      } else {
        var i = 0
        val n = weights.size / numOfLinearPredictor
        val weightsArray = weights.toArray
        while (i < numOfLinearPredictor) {
          //排除intercept
          val start = i * n
          val end = (i + 1) * n - { if (addIntercept) 1 else 0 }
          val partialWeightsArray = scaler.transform(
            Vectors.dense(weightsArray.slice(start, end))).toArray
          System.arraycopy(partialWeightsArray, 0, weightsArray, start, partialWeightsArray.size)
          i += 1
        }
        weights = Vectors.dense(weightsArray)
      }
    }
```

#### 5.1.4 创建模型

```scala
createModel(weights, intercept)
```

### 5.2 预测

&emsp;&emsp;训练完模型之后，我们就可以通过训练的模型计算得到测试数据的分类信息。`predictPoint`用来预测分类信息。它针对二分类和多分类，分别进行处理。

- 二分类的情况

```scala
val margin = dot(weightMatrix, dataMatrix) + intercept
val score = 1.0 / (1.0 + math.exp(-margin))
threshold match {
    case Some(t) => if (score > t) 1.0 else 0.0
    case None => score
}
```

&emsp;&emsp;我们可以看到`1.0 / (1.0 + math.exp(-margin))`就是上文提到的逻辑函数即`sigmoid`函数。

- 多分类情况

```scala
var bestClass = 0
var maxMargin = 0.0
val withBias = dataMatrix.size + 1 == dataWithBiasSize
(0 until numClasses - 1).foreach { i =>
        var margin = 0.0
        dataMatrix.foreachActive { (index, value) =>
          if (value != 0.0) margin += value * weightsArray((i * dataWithBiasSize) + index)
        }
        // Intercept is required to be added into margin.
        if (withBias) {
          margin += weightsArray((i * dataWithBiasSize) + dataMatrix.size)
        }
        if (margin > maxMargin) {
          maxMargin = margin
          bestClass = i + 1
        }
}
bestClass.toDouble
```

&emsp;&emsp;该段代码计算并找到最大的`margin`。如果`maxMargin`为负，那么第一类是该数据的类别。

# 参考文献

【1】[逻辑回归模型(Logistic Regression, LR)基础](http://www.cnblogs.com/sparkwen/p/3441197.html?utm_source=tuicool&utm_medium=referral)

【2】[逻辑回归](http://blog.csdn.net/pakko/article/details/37878837)

# 线性支持向量机

## 1 介绍

&emsp;&emsp;线性支持向量机是一个用于大规模分类任务的标准方法。它的目标函数[线性模型](#线性模型)中的公式（1）。它的损失函数是合页（`hinge`）损失，如下所示

<div  align="center"><img src="tech/ml/linear/lsvm-imgs/1.1.png" width = "220" height = "35" alt="1.1" align="center" /></div>

&emsp;&emsp;默认情况下，线性支持向量机训练时使用`L2`正则化。线性支持向量机输出一个`SVM`模型。给定一个新的数据点`x`，模型通过`w^Tx`的值预测，当这个值大于0时，输出为正，否则输出为负。

&emsp;&emsp;线性支持向量机并不需要核函数，要详细了解支持向量机，请参考文献【1】。

## 2 源码分析

### 2.1 实例

```scala
import org.apache.spark.mllib.classification.{SVMModel, SVMWithSGD}
import org.apache.spark.mllib.evaluation.BinaryClassificationMetrics
import org.apache.spark.mllib.util.MLUtils
// Load training data in LIBSVM format.
val data = MLUtils.loadLibSVMFile(sc, "data/mllib/sample_libsvm_data.txt")
// Split data into training (60%) and test (40%).
val splits = data.randomSplit(Array(0.6, 0.4), seed = 11L)
val training = splits(0).cache()
val test = splits(1)
// Run training algorithm to build the model
val numIterations = 100
val model = SVMWithSGD.train(training, numIterations)
// Clear the default threshold.
model.clearThreshold()
// Compute raw scores on the test set.
val scoreAndLabels = test.map { point =>
  val score = model.predict(point.features)
  (score, point.label)
}
// Get evaluation metrics.
val metrics = new BinaryClassificationMetrics(scoreAndLabels)
val auROC = metrics.areaUnderROC()
println("Area under ROC = " + auROC)
```

### 2.2 训练

&emsp;&emsp;和逻辑回归一样，训练过程均使用`GeneralizedLinearModel`中的`run`训练，只是训练使用的`Gradient`和`Updater`不同。在线性支持向量机中，使用`HingeGradient`计算梯度，使用`SquaredL2Updater`进行更新。
它的实现过程分为4步。参照[逻辑回归](#逻辑回归)了解这五步的详细情况。我们只需要了解`HingeGradient`和`SquaredL2Updater`的实现。

```scala
class HingeGradient extends Gradient {
  override def compute(data: Vector, label: Double, weights: Vector): (Vector, Double) = {
    val dotProduct = dot(data, weights)
    // 我们的损失函数是 max(0, 1 - (2y - 1) (f_w(x)))
    // 所以梯度是 -(2y - 1)*x
    val labelScaled = 2 * label - 1.0
    if (1.0 > labelScaled * dotProduct) {
      val gradient = data.copy
      scal(-labelScaled, gradient)
      (gradient, 1.0 - labelScaled * dotProduct)
    } else {
      (Vectors.sparse(weights.size, Array.empty, Array.empty), 0.0)
    }
  }

  override def compute(
      data: Vector,
      label: Double,
      weights: Vector,
      cumGradient: Vector): Double = {
    val dotProduct = dot(data, weights)
    // 我们的损失函数是 max(0, 1 - (2y - 1) (f_w(x)))
    // 所以梯度是 -(2y - 1)*x
    val labelScaled = 2 * label - 1.0
    if (1.0 > labelScaled * dotProduct) {
      //cumGradient -= labelScaled * data
      axpy(-labelScaled, data, cumGradient)
      //损失值
      1.0 - labelScaled * dotProduct
    } else {
      0.0
    }
  }
}
```

&emsp;&emsp;线性支持向量机的训练使用`L2`正则化方法。

```scala
class SquaredL2Updater extends Updater {
  override def compute(
      weightsOld: Vector,
      gradient: Vector,
      stepSize: Double,
      iter: Int,
      regParam: Double): (Vector, Double) = {
    // w' = w - thisIterStepSize * (gradient + regParam * w)
    // w' = (1 - thisIterStepSize * regParam) * w - thisIterStepSize * gradient
    //表示步长，即负梯度方向的大小
    val thisIterStepSize = stepSize / math.sqrt(iter)
    val brzWeights: BV[Double] = weightsOld.toBreeze.toDenseVector
    //正则化，brzWeights每行数据均乘以(1.0 - thisIterStepSize * regParam)
    brzWeights :*= (1.0 - thisIterStepSize * regParam)
    //y += x * a，即brzWeights -= gradient * thisInterStepSize
    brzAxpy(-thisIterStepSize, gradient.toBreeze, brzWeights)
    //正则化||w||_2
    val norm = brzNorm(brzWeights, 2.0)
    (Vectors.fromBreeze(brzWeights), 0.5 * regParam * norm * norm)
  }
}
```

&emsp;&emsp;该函数的实现规则是：

```scala
w' = w - thisIterStepSize * (gradient + regParam * w)
w' = (1 - thisIterStepSize * regParam) * w - thisIterStepSize * gradient
```

&emsp;&emsp;这里`thisIterStepSize`表示参数沿负梯度方向改变的速率，它随着迭代次数的增多而减小。

### 2.3 预测

```scala
override protected def predictPoint(
      dataMatrix: Vector,
      weightMatrix: Vector,
      intercept: Double) = {
    //w^Tx
    val margin = weightMatrix.toBreeze.dot(dataMatrix.toBreeze) + intercept
    threshold match {
      case Some(t) => if (margin > t) 1.0 else 0.0
      case None => margin
    }
  }
```

# 参考文献

【1】[支持向量机通俗导论（理解SVM的三层境界）](http://blog.csdn.net/macyang/article/details/38782399)

# 朴素贝叶斯

## 1 介绍

&emsp;&emsp;朴素贝叶斯是一种构建分类器的简单方法。该分类器模型会给问题实例分配用特征值表示的类标签，类标签取自有限集合。它不是训练这种分类器的单一算法，而是一系列基于相同原理的算法：**所有朴素贝叶斯分类器都假定样本每个特征与其他特征都不相关**。
举个例子，如果一种水果其具有红，圆，直径大概3英寸等特征，该水果可以被判定为是苹果。尽管这些特征相互依赖或者有些特征由其他特征决定，然而朴素贝叶斯分类器认为这些属性在判定该水果是否为苹果的概率分布上独立的。

&emsp;&emsp;对于某些类型的概率模型，在有监督学习的样本集中能获取得非常好的分类效果。在许多实际应用中，朴素贝叶斯模型参数估计使用最大似然估计方法；换言之，在不用贝叶斯概率或者任何贝叶斯模型的情况下，朴素贝叶斯模型也能奏效。

&emsp;&emsp;尽管是带着这些朴素思想和过于简单化的假设，但朴素贝叶斯分类器在很多复杂的现实情形中仍能够取得相当好的效果。尽管如此，有论文证明更新的方法（如提升树和随机森林）的性能超过了贝叶斯分类器。

&emsp;&emsp;朴素贝叶斯分类器的一个优势在于只需要根据少量的训练数据估计出必要的参数（变量的均值和方差）。由于变量独立假设，只需要估计各个变量，而不需要确定整个协方差矩阵。

### 1.1 朴素贝叶斯的优缺点

- 优点：学习和预测的效率高，且易于实现；在数据较少的情况下仍然有效，可以处理多分类问题。

- 缺点：分类效果不一定很高，特征独立性假设会是朴素贝叶斯变得简单，但是会牺牲一定的分类准确率。

## 2 朴素贝叶斯概率模型

&emsp;&emsp;理论上，概率模型分类器是一个条件概率模型。

<div  align="center"><img src="tech/ml/nb/imgs/1.1.png" width = "130" height = "20" alt="1.1" align="center" /></div>

&emsp;&emsp;独立的类别变量`C`有若干类别，条件依赖于若干特征变量`F_1,F_2,...,F_n`。但问题在于如果特征数量`n`较大或者每个特征能取大量值时，基于概率模型列出概率表变得不现实。所以我们修改这个模型使之变得可行。 贝叶斯定理有以下式子：

<div  align="center"><img src="tech/ml/nb/imgs/1.2.png" width = "350" height = "50" alt="1.2" align="center" /></div>

&emsp;&emsp;实际中，我们只关心分式中的分子部分，因为分母不依赖于`C`而且特征`F_i`的值是给定的，于是分母可以认为是一个常数。这样分子就等价于联合分布模型。
重复使用链式法则，可将该式写成条件概率的形式，如下所示：

<div  align="center"><img src="tech/ml/nb/imgs/1.3.png" width = "765" height = "137" alt="1.3" align="center" /></div>

&emsp;&emsp;现在“朴素”的条件独立假设开始发挥作用:假设每个特征`F_i`对于其他特征`F_j`是条件独立的。这就意味着

<div  align="center"><img src="tech/ml/nb/imgs/1.4.png" width = "180" height = "21" alt="1.4" align="center" /></div>

&emsp;&emsp;所以联合分布模型可以表达为

<div  align="center"><img src="tech/ml/nb/imgs/1.5.png" width = "450" height = "87" alt="1.5" align="center" /></div>

&emsp;&emsp;这意味着上述假设下，类变量`C`的条件分布可以表达为：

<div  align="center"><img src="tech/ml/nb/imgs/1.6.png" width = "310" height = "48" alt="1.6" align="center" /></div>

&emsp;&emsp;其中`Z`是一个只依赖与`F_1,...,F_n`等的缩放因子，当特征变量的值已知时是一个常数。

### 从概率模型中构造分类器

&emsp;&emsp;讨论至此为止我们导出了独立分布特征模型，也就是朴素贝叶斯概率模型。朴素贝叶斯分类器包括了这种模型和相应的决策规则。一个普通的规则就是选出最有可能的那个：这就是大家熟知的最大后验概率（`MAP`）决策准则。相应的分类器便是如下定义的公式：

<div  align="center"><img src="tech/ml/nb/imgs/1.7.png" width = "500" height = "55" alt="1.7" align="center" /></div>

## 3 参数估计

&emsp;&emsp;所有的模型参数都可以通过训练集的相关频率来估计。常用方法是概率的最大似然估计。类的先验概率`P(C)`可以通过假设各类等概率来计算`（先验概率 = 1 / (类的数量)）`，或者通过训练集的各类样本出现的次数来估计`（A类先验概率=（A类样本的数量）/(样本总数)）`。

&emsp;&emsp;对于类条件概率`P(X|c)`来说，直接根据样本出现的频率来估计会很困难。在现实应用中样本空间的取值往往远远大于训练样本数，也就是说，很多样本取值在训练集中根本没有出现，直接使用频率来估计`P(x|c)`不可行，因为"未被观察到"和"出现概率为零"是不同的。
为了估计特征的分布参数，我们要先假设训练集数据满足某种分布或者非参数模型。

&emsp;&emsp;这种假设称为朴素贝叶斯分类器的事件模型（`event model`）。对于离散的特征数据（例如文本分类中使用的特征），多元分布和伯努利分布比较流行。

### 3.1 高斯朴素贝叶斯

&emsp;&emsp;如果要处理的是连续数据，一种通常的假设是这些连续数值服从高斯分布。例如，假设训练集中有一个连续属性`x`。我们首先对数据根据类别分类，然后计算每个类别中`x`的均值和方差。令`mu_c`表示为`x`在`c`类上的均值，令`sigma^2_c`为`x`在`c`类上的方差。在给定类中某个值的概率
`P(x=v|c)`，可以通过将`v`表示为均值为`mu_c`，方差为`sigma^2_c`的正态分布计算出来。

<div  align="center"><img src="tech/ml/nb/imgs/1.8.png" width = "250" height = "60" alt="1.8" align="center" /></div>

&emsp;&emsp;处理连续数值问题的另一种常用的技术是通过离散化连续数值的方法。通常，当训练样本数量较少或者是精确的分布已知时，通过概率分布的方法是一种更好的选择。
在大量样本的情形下离散化的方法表现更优，因为大量的样本可以学习到数据的分布。由于朴素贝叶斯是一种典型的用到大量样本的方法（越大计算量的模型可以产生越高的分类精确度），所以朴素贝叶斯方法都用到离散化方法，而不是概率分布估计的方法。

### 3.2 多元朴素贝叶斯

&emsp;&emsp;在多元事件模型中，样本（特征向量）表示特定事件发生的次数。用`p_i`表示事件`i`发生的概率。特征向量`X=(x_1,x_2,...,x_n)`是一个`histogram`，其中`x_i`表示事件`i`在特定的对象中被观察到的次数。事件模型通常用于文本分类。相应的`x_i`表示词`i`在单个文档中出现的次数。
`X`的似然函数如下所示：

<div  align="center"><img src="tech/ml/nb/imgs/2.1.png" width = "230" height = "53" alt="2.1" align="center" /></div>

&emsp;&emsp;当用对数空间表达时，多元朴素贝叶斯分类器变成了线性分类器。

<div  align="center"><img src="tech/ml/nb/imgs/2.2.png" width = "340" height = "155" alt="2.2" align="center" /></div>

&emsp;&emsp;如果一个给定的类和特征值在训练集中没有一起出现过，那么基于频率的估计下该概率将为0。这将是一个问题。因为与其他概率相乘时将会把其他概率的信息统统去除。所以常常要求要对每个小类样本的概率估计进行修正，以保证不会出现有为0的概率出现。常用到的平滑就是加1平滑（也称拉普拉斯平滑）。

&emsp;&emsp;根据参考文献【2】，我们以文本分类的训练和测试为例子来介绍多元朴素贝叶斯的训练和测试过程。如下图所示。

<div  align="center"><img src="tech/ml/nb/imgs/2.3.png" width = "460" height = "390" alt="2.3" align="center" /></div>

&emsp;&emsp;这里的`CondProb[t][c]`即上文中的`P(x|C)`。`T_ct`表示类别为`c`的文档中`t`出现的次数。`+1`就是平滑手段。



### 3.3 伯努利朴素贝叶斯

&emsp;&emsp;在多变量伯努利事件模型中，特征是独立的二值变量。和多元模型一样，这个模型在文本分类中也非常流行。它的似然函数如下所示。

<div  align="center"><img src="tech/ml/nb/imgs/3.1.png" width = "260" height = "55" alt="3.1" align="center" /></div>

&emsp;&emsp;其中`p_ki`表示类别`C_k`生成`term` `w_i`的概率。这个模型通常用于短文本分类。

&emsp;&emsp;根据参考文献【2】，我们以文本分类的训练和测试为例子来介绍多元朴素贝叶斯的训练和测试过程。如下图所示。

<div  align="center"><img src="tech/ml/nb/imgs/3.2.png" width = "555" height = "450" alt="3.2" align="center" /></div>


## 4 源码分析

&emsp;&emsp;`MLlib`中实现了多元朴素贝叶斯和伯努利朴素贝叶斯。下面先看看朴素贝叶斯的使用实例。

### 4.1 实例

```scala
import org.apache.spark.mllib.classification.{NaiveBayes, NaiveBayesModel}
import org.apache.spark.mllib.linalg.Vectors
import org.apache.spark.mllib.regression.LabeledPoint
//读取并处理数据
val data = sc.textFile("data/mllib/sample_naive_bayes_data.txt")
val parsedData = data.map { line =>
  val parts = line.split(',')
  LabeledPoint(parts(0).toDouble, Vectors.dense(parts(1).split(' ').map(_.toDouble)))
}
// 切分数据为训练数据和测试数据
val splits = parsedData.randomSplit(Array(0.6, 0.4), seed = 11L)
val training = splits(0)
val test = splits(1)
//训练模型
val model = NaiveBayes.train(training, lambda = 1.0, modelType = "multinomial")
//测试数据
val predictionAndLabel = test.map(p => (model.predict(p.features), p.label))
val accuracy = 1.0 * predictionAndLabel.filter(x => x._1 == x._2).count() / test.count()
```

### 4.2 训练模型

&emsp;&emsp;从上文的原理分析我们可以知道，朴素贝叶斯模型的训练过程就是获取概率`p(C)`和`p(F|C)`的过程。根据`MLlib`的源码，我们可以将训练过程分为两步。
第一步是聚合计算每个标签对应的`term`的频率，第二步是迭代计算`p(C)`和`p(F|C)`。

- **1** 计算每个标签对应的`term`的频率

```scala
val aggregated = data.map(p => (p.label, p.features)).combineByKey[(Long, DenseVector)](
      createCombiner = (v: Vector) => {
        if (modelType == Bernoulli) {
          requireZeroOneBernoulliValues(v)
        } else {
          requireNonnegativeValues(v)
        }
        (1L, v.copy.toDense)
      },
      mergeValue = (c: (Long, DenseVector), v: Vector) => {
        requireNonnegativeValues(v)
        //c._2 = v*1 + c._2
        BLAS.axpy(1.0, v, c._2)
        (c._1 + 1L, c._2)
      },
      mergeCombiners = (c1: (Long, DenseVector), c2: (Long, DenseVector)) => {
        BLAS.axpy(1.0, c2._2, c1._2)
        (c1._1 + c2._1, c1._2)
      }
    //根据标签进行排序
    ).collect().sortBy(_._1)
```

&emsp;&emsp;这里我们需要先了解`createCombiner`函数的作用。`createCombiner`的作用是将原`RDD`中的`Vector`类型转换为`(long,Vector)`类型。

&emsp;&emsp;如果`modelType`为`Bernoulli`，那么`v`中包含的值只能为0或者1。如果`modelType`为`multinomial`，那么`v`中包含的值必须大于0。

```scala
//值非负
val requireNonnegativeValues: Vector => Unit = (v: Vector) => {
      val values = v match {
        case sv: SparseVector => sv.values
        case dv: DenseVector => dv.values
      }
      if (!values.forall(_ >= 0.0)) {
        throw new SparkException(s"Naive Bayes requires nonnegative feature values but found $v.")
      }
}
//值为0或者1
val requireZeroOneBernoulliValues: Vector => Unit = (v: Vector) => {
      val values = v match {
        case sv: SparseVector => sv.values
        case dv: DenseVector => dv.values
      }
      if (!values.forall(v => v == 0.0 || v == 1.0)) {
        throw new SparkException(
          s"Bernoulli naive Bayes requires 0 or 1 feature values but found $v.")
      }
}
```
&emsp;&emsp;`mergeValue`函数的作用是将新来的`Vector`累加到已有向量中，并更新词率。`mergeCombiners`则是合并不同分区的`(long,Vector)`数据。
通过这个函数，我们就找到了每个标签对应的词频率，并得到了标签对应的所有文档的累加向量。

- **2** 迭代计算`p(C)`和`p(F|C)`

```scala
//标签数
val numLabels = aggregated.length
//文档数
var numDocuments = 0L
aggregated.foreach { case (_, (n, _)) =>
  numDocuments += n
}
//特征维数
val numFeatures = aggregated.head match { case (_, (_, v)) => v.size }
val labels = new Array[Double](numLabels)
//表示logP(C)
val pi = new Array[Double](numLabels)
//表示logP(F|C)
val theta = Array.fill(numLabels)(new Array[Double](numFeatures))
val piLogDenom = math.log(numDocuments + numLabels * lambda)
var i = 0
aggregated.foreach { case (label, (n, sumTermFreqs)) =>
      labels(i) = label
      //训练步骤的第5步
      pi(i) = math.log(n + lambda) - piLogDenom
      val thetaLogDenom = modelType match {
        case Multinomial => math.log(sumTermFreqs.values.sum + numFeatures * lambda)
        case Bernoulli => math.log(n + 2.0 * lambda)
        case _ =>
          // This should never happen.
          throw new UnknownError(s"Invalid modelType: $modelType.")
      }
      //训练步骤的第6步
      var j = 0
      while (j < numFeatures) {
        theta(i)(j) = math.log(sumTermFreqs(j) + lambda) - thetaLogDenom
        j += 1
      }
      i += 1
    }
```
&emsp;&emsp;这段代码计算上文提到的`p(C)`和`p(F|C)`。这里的`lambda`表示平滑因子，一般情况下，我们将它设置为1。代码中，`p(c_i)=log (n+lambda)/(numDocs+numLabels*lambda)`，这对应上文训练过程的第5步`prior(c)=N_c/N`。

&emsp;&emsp;根据`modelType`类型的不同，`p(F|C)`的实现则不同。当`modelType`为`Multinomial`时，`P(F|C)=T_ct/sum(T_ct)`，这里`sum(T_ct)=sumTermFreqs.values.sum + numFeatures * lambda`。这对应多元朴素贝叶斯训练过程的第10步。
当`modelType`为`Bernoulli`时，`P(F|C)=(N_ct+lambda)/(N_c+2*lambda)`。这对应伯努利贝叶斯训练算法的第8行。

&emsp;&emsp;需要注意的是，代码中的所有计算都是取对数计算的。

## 4.3 预测数据

```scala
override def predict(testData: Vector): Double = {
    modelType match {
      case Multinomial =>
        labels(multinomialCalculation(testData).argmax)
      case Bernoulli =>
        labels(bernoulliCalculation(testData).argmax)
    }
}
```

&emsp;&emsp;预测也是根据`modelType`的不同作不同的处理。当`modelType`为`Multinomial`时，调用`multinomialCalculation`函数。

```scala
private def multinomialCalculation(testData: Vector) = {
    val prob = thetaMatrix.multiply(testData)
    BLAS.axpy(1.0, piVector, prob)
    prob
  }
```
&emsp;&emsp;这里的`thetaMatrix`和`piVector`即上文中训练得到的`P(F|C)`和`P(C)`，根据`P(C|F)=P(F|C)*P(C)`即可以得到预测数据归属于某类别的概率。
注意，这些概率都是基于对数结果计算的。

&emsp;&emsp;当`modelType`为`Bernoulli`时，实现代码略有不同。

```scala
private def bernoulliCalculation(testData: Vector) = {
    testData.foreachActive((_, value) =>
      if (value != 0.0 && value != 1.0) {
        throw new SparkException(
          s"Bernoulli naive Bayes requires 0 or 1 feature values but found $testData.")
      }
    )
    val prob = thetaMinusNegTheta.get.multiply(testData)
    BLAS.axpy(1.0, piVector, prob)
    BLAS.axpy(1.0, negThetaSum.get, prob)
    prob
  }
```

&emsp;&emsp;当词在训练数据中出现与否处理的过程不同。伯努利模型测试过程中，如果词存在，需要计算`log(condprob)`，否在需要计算`log(1-condprob)`，`condprob`为`P(f|c)=exp(theta)`。所以预先计算`log(1-exp(theta))`以及它的和可以应用到预测过程。这里`thetaMatrix`表示`logP(F|C)`，`negTheta`代表`log(1-exp(theta))=log(1-condprob)`，`thetaMinusNegTheta`代表`log(theta - log(1-exp(theta)))`。

```scala
 private val (thetaMinusNegTheta, negThetaSum) = modelType match {
    case Multinomial => (None, None)
    case Bernoulli =>
      val negTheta = thetaMatrix.map(value => math.log(1.0 - math.exp(value)))
      val ones = new DenseVector(Array.fill(thetaMatrix.numCols){1.0})
      val thetaMinusNegTheta = thetaMatrix.map { value =>
        value - math.log(1.0 - math.exp(value))
      }
      (Option(thetaMinusNegTheta), Option(negTheta.multiply(ones)))
    case _ =>
      // This should never happen.
      throw new UnknownError(s"Invalid modelType: $modelType.")
  }
```

&emsp;&emsp;这里`math.exp(value)`将对数概率恢复成真实的概率。

# 参考文献

【1】[朴素贝叶斯分类器](https://zh.wikipedia.org/wiki/%E6%9C%B4%E7%B4%A0%E8%B4%9D%E5%8F%B6%E6%96%AF%E5%88%86%E7%B1%BB%E5%99%A8)

【2】[Naive Bayes text classification](http://nlp.stanford.edu/IR-book/html/htmledition/naive-bayes-text-classification-1.html)

【3】[The Bernoulli model](http://nlp.stanford.edu/IR-book/html/htmledition/the-bernoulli-model-1.html)


# 决策树

## 1 决策树理论

### 1.1 什么是决策树

&emsp;&emsp;所谓决策树，顾名思义，是一种树，一种依托于策略抉择而建立起来的树。机器学习中，决策树是一个预测模型；他代表的是对象属性与对象值之间的一种映射关系。
树中每个节点表示某个对象，而每个分叉路径则代表的某个可能的属性值，从根节点到叶节点所经历的路径对应一个判定测试序列。决策树仅有单一输出，若欲有复数输出，可以建立独立的决策树以处理不同输出。

### 1.2 决策树学习流程

&emsp;&emsp;决策树学习的主要目的是为了产生一棵泛化能力强的决策树。其基本流程遵循简单而直接的“分而治之”的策略。它的流程实现如下所示：

```
输入：训练集 D={(x_1,y_1),(x_2,y_2),...,(x_m,y_m)};
      属性集 A={a_1,a_2,...,a_d}
过程：函数GenerateTree(D,A)
1: 生成节点node；
2: if D中样本全属于同一类别C then
3:    将node标记为C类叶节点，并返回
4: end if
5: if A为空 OR D中样本在A上取值相同 then
6:    将node标记为叶节点，其类别标记为D中样本数量最多的类，并返回
7: end if
8: 从A中选择最优划分属性 a*；    //每个属性包含若干取值，这里假设有v个取值
9: for a* 的每个值a*_v do
10:    为node生成一个分支，令D_v表示D中在a*上取值为a*_v的样本子集；
11:    if D_v 为空 then
12:       将分支节点标记为叶节点，其类别标记为D中样本最多的类，并返回
13:    else
14:       以GenerateTree(D_v,A\{a*})为分支节点
15:    end if
16: end for
```
&emsp;&emsp;决策树的生成是一个递归的过程。有三种情况会导致递归的返回：（1）当前节点包含的样本全属于同一个类别。（2）当前属性值为空，或者所有样本在所有属性上取相同的值。
（3）当前节点包含的样本集合为空。

&emsp;&emsp;在第（2）中情形下，我们把当前节点标记为叶节点，并将其类别设定为该节点所含样本最多的类别；在第（3）中情形下，同样把当前节点标记为叶节点，
但是将其类别设定为其父节点所含样本最多的类别。这两种处理实质不同，前者利用当前节点的后验分布，后者则把父节点的样本分布作为当前节点的先验分布。

### 1.3 决策树的构造

&emsp;&emsp;构造决策树的关键步骤是分裂属性（即确定属性的不同取值，对应上面流程中的`a_v`）。所谓分裂属性就是在某个节点处按照某一属性的不同划分构造不同的分支，其目标是让各个分裂子集尽可能地“纯”。
尽可能“纯”就是尽量让一个分裂子集中待分类项属于同一类别。分裂属性分为三种不同的情况：

- 1、属性是离散值且不要求生成二叉决策树。此时用属性的每一个划分作为一个分支。

- 2、属性是离散值且要求生成二叉决策树。此时使用属性划分的一个子集进行测试，按照“属于此子集”和“不属于此子集”分成两个分支。

- 3、属性是连续值。此时确定一个值作为分裂点`split_point`，按照`>split_point`和`<=split_point`生成两个分支。

### 1.4 划分选择

&emsp;&emsp;在决策树算法中，如何选择最优划分属性是最关键的一步。一般而言，随着划分过程的不断进行，我们希望决策树的分支节点所包含的样本尽可能属于同一类别，即节点的“纯度(purity)”越来越高。
有几种度量样本集合纯度的指标。在`MLlib`中，信息熵和基尼指数用于决策树分类，方差用于决策树回归。

#### 1.4.1 信息熵

&emsp;&emsp;信息熵是度量样本集合纯度最常用的一种指标，假设当前样本集合`D`中第`k`类样本所占的比例为`p_k`，则`D`的信息熵定义为：

<div  align="center"><img src="tech/ml/decision/imgs/1.1.png" width = "220" height = "75" alt="1.1" align="center" /></div>

&emsp;&emsp;`Ent(D)`的值越小，则`D`的纯度越高。

#### 1.4.2 基尼系数

&emsp;&emsp;采用和上式相同的符号，基尼系数可以用来度量数据集`D`的纯度。

<div  align="center"><img src="tech/ml/decision/imgs/1.2.png" width = "310" height = "90" alt="1.2" align="center" /></div>

&emsp;&emsp;直观来说，`Gini(D)`反映了从数据集`D`中随机取样两个样本，其类别标记不一致的概率。因此，`Gini(D)`越小，则数据集`D`的纯度越高。

#### 1.4.3 方差

&emsp;&emsp;`MLlib`中使用方差来度量纯度。如下所示

<div  align="center"><img src="tech/ml/decision/imgs/1.3.png" width = "255" height = "70" alt="1.3" align="center" /></div>

#### 1.4.4 信息增益

&emsp;&emsp;假设切分大小为`N`的数据集`D`为两个数据集`D_left`和`D_right`，那么信息增益可以表示为如下的形式。

<div  align="center"><img src="tech/ml/decision/imgs/1.4.png" width = "600" height = "60" alt="1.4" align="center" /></div>

&emsp;&emsp;一般情况下，信息增益越大，则意味着使用属性`a`来进行划分所获得的纯度提升越大。因此我们可以用信息增益来进行决策树的划分属性选择。即流程中的第8步。

### 1.5 决策树的优缺点

**决策树的优点：**

- 1 决策树易于理解和解释；
- 2 能够同时处理数据型和类别型属性；
- 3 决策树是一个白盒模型，给定一个观察模型，很容易推出相应的逻辑表达式；
- 4 在相对较短的时间内能够对大型数据作出效果良好的结果；
- 5 比较适合处理有缺失属性值的样本。

**决策树的缺点：**

- 1 对那些各类别数据量不一致的数据，在决策树种，信息增益的结果偏向那些具有更多数值的特征；
- 2 容易过拟合；
- 3 忽略了数据集中属性之间的相关性。


## 2 实例与源码分析

### 2.1 实例

&emsp;&emsp;下面的例子用于分类。

```scala
import org.apache.spark.mllib.tree.DecisionTree
import org.apache.spark.mllib.tree.model.DecisionTreeModel
import org.apache.spark.mllib.util.MLUtils
// Load and parse the data file.
val data = MLUtils.loadLibSVMFile(sc, "data/mllib/sample_libsvm_data.txt")
// Split the data into training and test sets (30% held out for testing)
val splits = data.randomSplit(Array(0.7, 0.3))
val (trainingData, testData) = (splits(0), splits(1))
// Train a DecisionTree model.
//  Empty categoricalFeaturesInfo indicates all features are continuous.
val numClasses = 2
val categoricalFeaturesInfo = Map[Int, Int]()
val impurity = "gini"
val maxDepth = 5
val maxBins = 32
val model = DecisionTree.trainClassifier(trainingData, numClasses, categoricalFeaturesInfo,
  impurity, maxDepth, maxBins)
// Evaluate model on test instances and compute test error
val labelAndPreds = testData.map { point =>
  val prediction = model.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count().toDouble / testData.count()
println("Test Error = " + testErr)
println("Learned classification tree model:\n" + model.toDebugString)
```

&emsp;&emsp;下面的例子用于回归。

```scala
import org.apache.spark.mllib.tree.DecisionTree
import org.apache.spark.mllib.tree.model.DecisionTreeModel
import org.apache.spark.mllib.util.MLUtils
// Load and parse the data file.
val data = MLUtils.loadLibSVMFile(sc, "data/mllib/sample_libsvm_data.txt")
// Split the data into training and test sets (30% held out for testing)
val splits = data.randomSplit(Array(0.7, 0.3))
val (trainingData, testData) = (splits(0), splits(1))
// Train a DecisionTree model.
//  Empty categoricalFeaturesInfo indicates all features are continuous.
val categoricalFeaturesInfo = Map[Int, Int]()
val impurity = "variance"
val maxDepth = 5
val maxBins = 32
val model = DecisionTree.trainRegressor(trainingData, categoricalFeaturesInfo, impurity,
  maxDepth, maxBins)
// Evaluate model on test instances and compute test error
val labelsAndPredictions = testData.map { point =>
  val prediction = model.predict(point.features)
  (point.label, prediction)
}
val testMSE = labelsAndPredictions.map{ case (v, p) => math.pow(v - p, 2) }.mean()
println("Test Mean Squared Error = " + testMSE)
println("Learned regression tree model:\n" + model.toDebugString)
```

### 2.2 源码分析

&emsp;&emsp;在`MLlib`中，决策树的实现和随机森林的实现是在一起的。随机森林实现中，当树的个数为1时，它的实现即为决策树的实现。

```scala
def run(input: RDD[LabeledPoint]): DecisionTreeModel = {
    //树个数为1
    val rf = new RandomForest(strategy, numTrees = 1, featureSubsetStrategy = "all", seed = 0)
    val rfModel = rf.run(input)
    rfModel.trees(0)
  }
```

&emsp;&emsp;这里的`strategy`是`Strategy`的实例，它包含如下信息：

```scala
/**
 * Stores all the configuration options for tree construction
 * @param algo  Learning goal.  Supported:
 *              [[org.apache.spark.mllib.tree.configuration.Algo.Classification]],
 *              [[org.apache.spark.mllib.tree.configuration.Algo.Regression]]
 * @param impurity Criterion used for information gain calculation.
 *                 Supported for Classification: [[org.apache.spark.mllib.tree.impurity.Gini]],
 *                  [[org.apache.spark.mllib.tree.impurity.Entropy]].
 *                 Supported for Regression: [[org.apache.spark.mllib.tree.impurity.Variance]].
 * @param maxDepth Maximum depth of the tree.
 *                 E.g., depth 0 means 1 leaf node; depth 1 means 1 internal node + 2 leaf nodes.
 * @param numClasses Number of classes for classification.
 *                                    (Ignored for regression.)
 *                                    Default value is 2 (binary classification).
 * @param maxBins Maximum number of bins used for discretizing continuous features and
 *                for choosing how to split on features at each node.
 *                More bins give higher granularity.
 * @param quantileCalculationStrategy Algorithm for calculating quantiles.  Supported:
 *                             [[org.apache.spark.mllib.tree.configuration.QuantileStrategy.Sort]]
 * @param categoricalFeaturesInfo A map storing information about the categorical variables and the
 *                                number of discrete values they take. For example, an entry (n ->
 *                                k) implies the feature n is categorical with k categories 0,
 *                                1, 2, ... , k-1. It's important to note that features are
 *                                zero-indexed.
 * @param minInstancesPerNode Minimum number of instances each child must have after split.
 *                            Default value is 1. If a split cause left or right child
 *                            to have less than minInstancesPerNode,
 *                            this split will not be considered as a valid split.
 * @param minInfoGain Minimum information gain a split must get. Default value is 0.0.
 *                    If a split has less information gain than minInfoGain,
 *                    this split will not be considered as a valid split.
 * @param maxMemoryInMB Maximum memory in MB allocated to histogram aggregation. Default value is
 *                      256 MB.
 * @param subsamplingRate Fraction of the training data used for learning decision tree.
 * @param useNodeIdCache If this is true, instead of passing trees to executors, the algorithm will
 *                      maintain a separate RDD of node Id cache for each row.
 * @param checkpointInterval How often to checkpoint when the node Id cache gets updated.
 *                           E.g. 10 means that the cache will get checkpointed every 10 updates. If
 *                           the checkpoint directory is not set in
 *                           [[org.apache.spark.SparkContext]], this setting is ignored.
 */
class Strategy @Since("1.3.0") (
    @Since("1.0.0") @BeanProperty var algo: Algo,//选择的算法，有分类和回归两种选择
    @Since("1.0.0") @BeanProperty var impurity: Impurity,//纯度有熵、基尼系数、方差三种选择
    @Since("1.0.0") @BeanProperty var maxDepth: Int,//树的最大深度
    @Since("1.2.0") @BeanProperty var numClasses: Int = 2,//分类数
    @Since("1.0.0") @BeanProperty var maxBins: Int = 32,//最大子树个数
    @Since("1.0.0") @BeanProperty var quantileCalculationStrategy: QuantileStrategy = Sort,
    //保存类别变量以及相应的离散值。一个entry (n ->k) 表示特征n属于k个类别，分别是0,1,...,k-1
    @Since("1.0.0") @BeanProperty var categoricalFeaturesInfo: Map[Int, Int] = Map[Int, Int](),
    @Since("1.2.0") @BeanProperty var minInstancesPerNode: Int = 1,
    @Since("1.2.0") @BeanProperty var minInfoGain: Double = 0.0,
    @Since("1.0.0") @BeanProperty var maxMemoryInMB: Int = 256,
    @Since("1.2.0") @BeanProperty var subsamplingRate: Double = 1,
    @Since("1.2.0") @BeanProperty var useNodeIdCache: Boolean = false,
    @Since("1.2.0") @BeanProperty var checkpointInterval: Int = 10) extends Serializable
```

&emsp;&emsp;决策树的实现我们在[随机森林](../组合树/随机森林/random-forests.md)专题介绍。这里我们只需要知道，当随机森林的树个数为1时，它即为决策树，
并且此时，树的训练所用的特征是全部特征，而不是随机选择的部分特征。即`featureSubsetStrategy = "all"`。

# 集成学习

&emsp;&emsp;集成学习通过构建并结合多个学习器来完成学习任务，有时也被称为多分类器系统。集成学习通过将多个学习器进行结合，常可获得比单一学习器显著优越的泛化能力。

&emsp;&emsp;根据个体学习器的生成方式，目前的集成学习方法大致可以分为两大类。即个体学习器之间存在强依赖性，必须串行生成的序列化方法以及个体学习器之间不存在强依赖性，可同时生成的并行化方法。
前者的代表是`Boosting`，后者的代表是`Bagging`和随机森林。后面的随机森林章节会详细介绍`Bagging`和随机森林；梯度提升树章节会详细介绍`Boosting`和梯度提升树。

# 梯度提升树

## 1 Boosting

&emsp;&emsp;`Boosting`是一类将弱学习器提升为强学习器的算法。这类算法的工作机制类似：先从初始训练集中训练出一个基学习器，再根据基学习器的表现对训练样本分布进行调整，使得先前基学习器做错的训练样本在后续受到更多关注。
然后基于调整后的样本分布来训练下一个基学习器；如此重复进行，直至基学习器的数目达到事先指定的值`T`，最终将这`T`个基学习器进行加权结合。

&emsp;&emsp;`Boost`算法是在算法开始时，为每一个样本赋上一个相等的权重值，也就是说，最开始的时候，大家都是一样重要的。
在每一次训练中得到的模型，会使得数据点的估计有所差异，所以在每一步结束后，我们需要对权重值进行处理，而处理的方式就是通过**增加错分点的权重**，这样使得某些点如果老是被分错，那么就会被“严重关注”，也就被赋上一个很高的权重。
然后等进行了`N`次迭代，将会得到`N`个简单的基分类器（`basic learner`），最后将它们组合起来，可以对它们进行加权（错误率越大的基分类器权重值越小，错误率越小的基分类器权重值越大）、或者让它们进行投票等得到一个最终的模型。

&emsp;&emsp;梯度提升（`gradient boosting`）属于`Boost`算法的一种，也可以说是`Boost`算法的一种改进，它与传统的`Boost`有着很大的区别，它的每一次计算都是为了减少上一次的残差(`residual`)，而为了减少这些残差，可以在残差减少的梯度(`Gradient`)方向上建立一个新模型。所以说，在`Gradient Boost`中，每个新模型的建立是为了使得先前模型残差往梯度方向减少，
与传统的`Boost`算法对正确、错误的样本进行加权有着极大的区别。

&emsp;&emsp;梯度提升算法的核心在于，每棵树是从先前所有树的残差中来学习。**利用的是当前模型中损失函数的负梯度值作为提升树算法中的残差的近似值**，进而拟合一棵回归（分类）树。

## 2 梯度提升

&emsp;&emsp;根据参考文献【1】的介绍，梯度提升算法的算法流程如下所示：

<div  align="center"><img src="imgs/1.1.png" width = "700" height = "300" alt="1.1" align="center" /></div>

&emsp;&emsp;在上述的流程中，`F(x)`表示学习器，`psi`表示损失函数，第3行的`y_im`表示负梯度方向，第4行的`R_lm`表示原数据改变分布后的数据。

&emsp;&emsp;在`MLlib`中，提供的损失函数有三种。如下图所示。

<div  align="center"><img src="imgs/1.3.png" width = "700" height = "150" alt="1.3" align="center" /></div>

&emsp;&emsp;第一个对数损失用于分类，后两个平方误差和绝对误差用于回归。

## 3 随机梯度提升

&emsp;&emsp;有文献证明，注入随机性到上述的过程中可以提高函数估计的性能。受到`Breiman`的影响，将随机性作为一个考虑的因素。在每次迭代中，随机的在训练集中抽取一个子样本集，然后在后续的操作中用这个子样本集代替全体样本。
这就形成了随机梯度提升算法。它的流程如下所示：

<div  align="center"><img src="imgs/1.2.png" width = "700" height = "300" alt="1.2" align="center" /></div>

## 4 实例

&emsp;&emsp;下面的代码是分类的例子。

```scala
import org.apache.spark.mllib.tree.GradientBoostedTrees
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.GradientBoostedTreesModel
import org.apache.spark.mllib.util.MLUtils
// 准备数据
val data = MLUtils.loadLibSVMFile(sc, "data/mllib/sample_libsvm_data.txt")
// Split the data into training and test sets (30% held out for testing)
val splits = data.randomSplit(Array(0.7, 0.3))
val (trainingData, testData) = (splits(0), splits(1))
// 训练模型
// The defaultParams for Classification use LogLoss by default.
val boostingStrategy = BoostingStrategy.defaultParams("Classification")
boostingStrategy.numIterations = 3 // Note: Use more iterations in practice.
boostingStrategy.treeStrategy.numClasses = 2
boostingStrategy.treeStrategy.maxDepth = 5
// Empty categoricalFeaturesInfo indicates all features are continuous.
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]()
val model = GradientBoostedTrees.train(trainingData, boostingStrategy)
// 用测试数据评价模型
val labelAndPreds = testData.map { point =>
  val prediction = model.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / testData.count()
println("Test Error = " + testErr)
println("Learned classification GBT model:\n" + model.toDebugString)
```
&emsp;&emsp;下面的代码是回归的例子。

```scala
import org.apache.spark.mllib.tree.GradientBoostedTrees
import org.apache.spark.mllib.tree.configuration.BoostingStrategy
import org.apache.spark.mllib.tree.model.GradientBoostedTreesModel
import org.apache.spark.mllib.util.MLUtils
// 准备数据
val data = MLUtils.loadLibSVMFile(sc, "data/mllib/sample_libsvm_data.txt")
// Split the data into training and test sets (30% held out for testing)
val splits = data.randomSplit(Array(0.7, 0.3))
val (trainingData, testData) = (splits(0), splits(1))
// 训练模型
// The defaultParams for Regression use SquaredError by default.
val boostingStrategy = BoostingStrategy.defaultParams("Regression")
boostingStrategy.numIterations = 3 // Note: Use more iterations in practice.
boostingStrategy.treeStrategy.maxDepth = 5
// Empty categoricalFeaturesInfo indicates all features are continuous.
boostingStrategy.treeStrategy.categoricalFeaturesInfo = Map[Int, Int]()
val model = GradientBoostedTrees.train(trainingData, boostingStrategy)
// 用测试数据评价模型
val labelsAndPredictions = testData.map { point =>
  val prediction = model.predict(point.features)
  (point.label, prediction)
}
val testMSE = labelsAndPredictions.map{ case(v, p) => math.pow((v - p), 2)}.mean()
println("Test Mean Squared Error = " + testMSE)
println("Learned regression GBT model:\n" + model.toDebugString)
```

## 5 源码分析

### 5.1 训练分析

&emsp;&emsp;梯度提升树的训练从`run`方法开始。

```scala
def run(input: RDD[LabeledPoint]): GradientBoostedTreesModel = {
    val algo = boostingStrategy.treeStrategy.algo
    algo match {
      case Regression =>
        GradientBoostedTrees.boost(input, input, boostingStrategy, validate = false)
      case Classification =>
        // Map labels to -1, +1 so binary classification can be treated as regression.
        val remappedInput = input.map(x => new LabeledPoint((x.label * 2) - 1, x.features))
        GradientBoostedTrees.boost(remappedInput, remappedInput, boostingStrategy, validate = false)
      case _ =>
        throw new IllegalArgumentException(s"$algo is not supported by the gradient boosting.")
    }
}
```

&emsp;&emsp;在`MLlib`中，梯度提升树只能用于二分类和回归。所以，在上面的代码中，将标签映射为`-1,+1`，那么二分类也可以被当做回归。整个训练过程在`GradientBoostedTrees.boost`中实现。
`GradientBoostedTrees.boost`的过程分为三步，第一步，初始化参数；第二步，训练第一棵树；第三步，迭代训练后续的树。下面分别介绍这三步。

- 初始化参数

```scala
// 初始化梯度提升参数
// 迭代次数，默认为100
val numIterations = boostingStrategy.numIterations
// 基学习器
val baseLearners = new Array[DecisionTreeModel](numIterations)
// 基学习器权重
val baseLearnerWeights = new Array[Double](numIterations)
// 损失函数，分类时，用对数损失，回归时，用误差平方损失
val loss = boostingStrategy.loss
val learningRate = boostingStrategy.learningRate
// Prepare strategy for individual trees, which use regression with variance impurity.
// 回归时，使用方差计算不纯度
val treeStrategy = boostingStrategy.treeStrategy.copy
val validationTol = boostingStrategy.validationTol
treeStrategy.algo = Regression
treeStrategy.impurity = Variance
// 缓存输入数据
val persistedInput = if (input.getStorageLevel == StorageLevel.NONE) {
   input.persist(StorageLevel.MEMORY_AND_DISK)
   true
} else {
   false
}
// Prepare periodic checkpointers
val predErrorCheckpointer = new PeriodicRDDCheckpointer[(Double, Double)](
    treeStrategy.getCheckpointInterval, input.sparkContext)
val validatePredErrorCheckpointer = new PeriodicRDDCheckpointer[(Double, Double)](
    treeStrategy.getCheckpointInterval, input.sparkContext)
```

- 训练第一棵树（即第一个基学习器）

```scala
//通过训练数据训练出一颗决策树，具体信息请参考随机森林的分析
val firstTreeModel = new DecisionTree(treeStrategy).run(input)
val firstTreeWeight = 1.0
baseLearners(0) = firstTreeModel
baseLearnerWeights(0) = firstTreeWeight
var predError: RDD[(Double, Double)] = GradientBoostedTreesModel.
      computeInitialPredictionAndError(input, firstTreeWeight, firstTreeModel, loss)
predErrorCheckpointer.update(predError)
```

&emsp;&emsp;这里比较关键的是通过`GradientBoostedTreesModel.computeInitialPredictionAndError`计算初始的预测和误差。

```scala
def computeInitialPredictionAndError(
      data: RDD[LabeledPoint],
      initTreeWeight: Double,
      initTree: DecisionTreeModel,
      loss: Loss): RDD[(Double, Double)] = {
    data.map { lp =>
      val pred = initTreeWeight * initTree.predict(lp.features)
      val error = loss.computeError(pred, lp.label)
      (pred, error)
    }
  }
```

&emsp;&emsp;根据选择的损失函数的不同，`computeError`的实现不同。

```scala
//对数损失的实现
override private[mllib] def computeError(prediction: Double, label: Double): Double = {
    val margin = 2.0 * label * prediction
    // The following is equivalent to 2.0 * log(1 + exp(-margin)) but more numerically stable.
    2.0 * MLUtils.log1pExp(-margin)
}
//误差平方损失
override private[mllib] def computeError(prediction: Double, label: Double): Double = {
    val err = label - prediction
    err * err
}
```

- 迭代训练后续树

```scala
var validatePredError: RDD[(Double, Double)] = GradientBoostedTreesModel.
      computeInitialPredictionAndError(validationInput, firstTreeWeight, firstTreeModel, loss)
if (validate) validatePredErrorCheckpointer.update(validatePredError)
var bestValidateError = if (validate) validatePredError.values.mean() else 0.0
var bestM = 1
var m = 1
var doneLearning = false
while (m < numIterations && !doneLearning) {
    // Update data with pseudo-residuals
    // 根据梯度调整训练数据
    val data = predError.zip(input).map { case ((pred, _), point) =>
      //标签为上一棵树预测的数据的负梯度方向
      LabeledPoint(-loss.gradient(pred, point.label), point.features)
    }
    //训练下一棵树
    val model = new DecisionTree(treeStrategy).run(data)
    // Update partial model
    baseLearners(m) = model
    // Note: The setting of baseLearnerWeights is incorrect for losses other than SquaredError.
    //       Technically, the weight should be optimized for the particular loss.
    //       However, the behavior should be reasonable, though not optimal.
    baseLearnerWeights(m) = learningRate
    //更新预测和误差
    predError = GradientBoostedTreesModel.updatePredictionError(
        input, predError, baseLearnerWeights(m), baseLearners(m), loss)
    predErrorCheckpointer.update(predError)
    //当需要验证阈值，提前终止迭代时
    if (validate) {
        // Stop training early if
        // 1. Reduction in error is less than the validationTol or
        // 2. If the error increases, that is if the model is overfit.
        // We want the model returned corresponding to the best validation error.
        validatePredError = GradientBoostedTreesModel.updatePredictionError(
          validationInput, validatePredError, baseLearnerWeights(m), baseLearners(m), loss)
        validatePredErrorCheckpointer.update(validatePredError)
        val currentValidateError = validatePredError.values.mean()
        if (bestValidateError - currentValidateError < validationTol * Math.max(
          currentValidateError, 0.01)) {
          doneLearning = true
        } else if (currentValidateError < bestValidateError) {
          bestValidateError = currentValidateError
          bestM = m + 1
        }
    }
    m += 1
}
```

&emsp;&emsp;上面代码最重要的部分是更新预测和误差的实现。通过`GradientBoostedTreesModel.updatePredictionError`实现。

```scala
 def updatePredictionError(
    data: RDD[LabeledPoint],
    predictionAndError: RDD[(Double, Double)],
    treeWeight: Double,
    tree: DecisionTreeModel,
    loss: Loss): RDD[(Double, Double)] = {
    val newPredError = data.zip(predictionAndError).mapPartitions { iter =>
      iter.map { case (lp, (pred, error)) =>
        val newPred = pred + tree.predict(lp.features) * treeWeight
        val newError = loss.computeError(newPred, lp.label)
        (newPred, newError)
      }
    }
    newPredError
  }
```

### 5.2 测试

&emsp;&emsp;利用梯度提升树进行预测时，调用的`predict`方法扩展自`TreeEnsembleModel`，它是树结构组合模型的表示，其核心代码如下所示：

```scala
//不同的策略采用不同的预测方法
def predict(features: Vector): Double = {
    (algo, combiningStrategy) match {
      case (Regression, Sum) =>
        predictBySumming(features)
      case (Regression, Average) =>
        predictBySumming(features) / sumWeights
      //用于梯度提升树，转换为1 或者 0
      case (Classification, Sum) => // binary classification
        val prediction = predictBySumming(features)
        // TODO: predicted labels are +1 or -1 for GBT. Need a better way to store this info.
        if (prediction > 0.0) 1.0 else 0.0
      case (Classification, Vote) =>
        predictByVoting(features)
      case _ =>
        throw new IllegalArgumentException()
    }
}
private def predictBySumming(features: Vector): Double = {
    val treePredictions = trees.map(_.predict(features))
    //两个向量的内集
    blas.ddot(numTrees, treePredictions, 1, treeWeights, 1)
}
```

# 参考文献

【1】[Stochastic Gradient Boost](https://statweb.stanford.edu/~jhf/ftp/stobst.pdf)

【2】[机器学习算法-梯度树提升GTB（GBRT）](http://www.07net01.com/2015/08/918187.html)

# 随机森林

## 1 Bagging

&emsp;&emsp;`Bagging`采用自助采样法(`bootstrap sampling`)采样数据。给定包含`m`个样本的数据集，我们先随机取出一个样本放入采样集中，再把该样本放回初始数据集，使得下次采样时，样本仍可能被选中，
这样，经过`m`次随机采样操作，我们得到包含`m`个样本的采样集。

&emsp;&emsp;按照此方式，我们可以采样出`T`个含`m`个训练样本的采样集，然后基于每个采样集训练出一个基本学习器，再将这些基本学习器进行结合。这就是`Bagging`的一般流程。在对预测输出进行结合时，`Bagging`通常使用简单投票法，
对回归问题使用简单平均法。若分类预测时，出现两个类收到同样票数的情形，则最简单的做法是随机选择一个，也可以进一步考察学习器投票的置信度来确定最终胜者。

&emsp;&emsp;`Bagging`的算法描述如下图所示。

<div  align="center"><img src="tech/ml/tree/random/imgs/1.1.png" width = "400" height = "220" alt="1.1" align="center" /></div>

## 2 随机森林

&emsp;&emsp;随机森林是`Bagging`的一个扩展变体。随机森林在以决策树为基学习器构建`Bagging`集成的基础上，进一步在决策树的训练过程中引入了随机属性选择。具体来讲，传统决策树在选择划分属性时，
在当前节点的属性集合（假设有`d`个属性）中选择一个最优属性；而在随机森林中，对基决策树的每个节点，先从该节点的属性集合中随机选择一个包含`k`个属性的子集，然后再从这个子集中选择一个最优属性用于划分。
这里的参数`k`控制了随机性的引入程度。若令`k=d`，则基决策树的构建与传统决策树相同；若令`k=1`，则是随机选择一个属性用于划分。在`MLlib`中，有两种选择用于分类，即`k=log2(d)`、`k=sqrt(d)`；
一种选择用于回归，即`k=1/3d`。在源码分析中会详细介绍。

&emsp;&emsp;可以看出，随机森林对`Bagging`只做了小改动，但是与`Bagging`中基学习器的“多样性”仅仅通过样本扰动（通过对初始训练集采样）而来不同，随机森林中基学习器的多样性不仅来自样本扰动，还来自属性扰动。
这使得最终集成的泛化性能可通过个体学习器之间差异度的增加而进一步提升。

## 3 随机森林在分布式环境下的优化策略

&emsp;&emsp;随机森林算法在单机环境下很容易实现，但在分布式环境下特别是在`Spark`平台上，传统单机形式的迭代方式必须要进行相应改进才能适用于分布式环境
，这是因为在分布式环境下，数据也是分布式的，算法设计不得当会生成大量的`IO`操作，例如频繁的网络数据传输，从而影响算法效率。
因此，在`Spark`上进行随机森林算法的实现，需要进行一定的优化，`Spark`中的随机森林算法主要实现了三个优化策略：

- 切分点抽样统计，如下图所示。在单机环境下的决策树对连续变量进行切分点选择时，一般是通过对特征点进行排序，然后取相邻两个数之间的点作为切分点，这在单机环境下是可行的，但如果在分布式环境下如此操作的话，
会带来大量的网络传输操作，特别是当数据量达到`PB`级时，算法效率将极为低下。为避免该问题，`Spark`中的随机森林在构建决策树时，会对各分区采用一定的子特征策略进行抽样，然后生成各个分区的统计数据，并最终得到切分点。
(从源代码里面看，是先对样本进行抽样，然后根据抽样样本值出现的次数进行排序，然后再进行切分)。

<div  align="center"><img src="tech/ml/tree/random/imgs/1.2.png" width = "600" height = "350" alt="1.2" align="center" /></div>

- 特征装箱（`Binning`），如下图所示。决策树的构建过程就是对特征的取值不断进行划分的过程，对于离散的特征，如果有`M`个值，最多有`2^(M-1) - 1`个划分。如果值是有序的，那么就最多`M-1`个划分。
比如年龄特征，有老，中，少3个值，如果无序有`2^2-1=3`个划分，即`老|中，少；老，中|少；老，少|中`。；如果是有序的，即按老，中，少的序，那么只有`m-1`个，即2种划分，`老|中，少；老，中|少`。
对于连续的特征，其实就是进行范围划分，而划分的点就是`split`（切分点），划分出的区间就是`bin`。对于连续特征，理论上`split`是无数的，在分布环境下不可能取出所有的值，因此它采用的是切点抽样统计方法。

<div  align="center"><img src="tech/ml/tree/random/imgs/1.3.png" width = "600" height = "400" alt="1.3" align="center" /></div>

- 逐层训练（`level-wise training`），如下图所示。单机版本的决策树生成过程是通过递归调用（本质上是深度优先）的方式构造树，在构造树的同时，需要移动数据，将同一个子节点的数据移动到一起。
此方法在分布式数据结构上无法有效的执行，而且也无法执行，因为数据太大，无法放在一起，所以在分布式环境下采用的策略是逐层构建树节点（本质上是广度优先），这样遍历所有数据的次数等于所有树中的最大层数。
每次遍历时，只需要计算每个节点所有切分点统计参数，遍历完后，根据节点的特征划分，决定是否切分，以及如何切分。

<div  align="center"><img src="tech/ml/tree/random/imgs/1.4.png" width = "600" height = "350" alt="1.4" align="center" /></div>

## 4 使用实例

&emsp;&emsp;下面的例子用于分类。

```scala
import org.apache.spark.mllib.tree.RandomForest
import org.apache.spark.mllib.tree.model.RandomForestModel
import org.apache.spark.mllib.util.MLUtils
// Load and parse the data file.
val data = MLUtils.loadLibSVMFile(sc, "data/mllib/sample_libsvm_data.txt")
// Split the data into training and test sets (30% held out for testing)
val splits = data.randomSplit(Array(0.7, 0.3))
val (trainingData, testData) = (splits(0), splits(1))
// Train a RandomForest model.
// 空的类别特征信息表示所有的特征都是连续的.
val numClasses = 2
val categoricalFeaturesInfo = Map[Int, Int]()
val numTrees = 3 // Use more in practice.
val featureSubsetStrategy = "auto" // Let the algorithm choose.
val impurity = "gini"
val maxDepth = 4
val maxBins = 32
val model = RandomForest.trainClassifier(trainingData, numClasses, categoricalFeaturesInfo,
  numTrees, featureSubsetStrategy, impurity, maxDepth, maxBins)
// Evaluate model on test instances and compute test error
val labelAndPreds = testData.map { point =>
  val prediction = model.predict(point.features)
  (point.label, prediction)
}
val testErr = labelAndPreds.filter(r => r._1 != r._2).count.toDouble / testData.count()
println("Test Error = " + testErr)
println("Learned classification forest model:\n" + model.toDebugString)
```

&emsp;&emsp;下面的例子用于回归。

```scala
import org.apache.spark.mllib.tree.RandomForest
import org.apache.spark.mllib.tree.model.RandomForestModel
import org.apache.spark.mllib.util.MLUtils
// Load and parse the data file.
val data = MLUtils.loadLibSVMFile(sc, "data/mllib/sample_libsvm_data.txt")
// Split the data into training and test sets (30% held out for testing)
val splits = data.randomSplit(Array(0.7, 0.3))
val (trainingData, testData) = (splits(0), splits(1))
// Train a RandomForest model.
// 空的类别特征信息表示所有的特征都是连续的
val numClasses = 2
val categoricalFeaturesInfo = Map[Int, Int]()
val numTrees = 3 // Use more in practice.
val featureSubsetStrategy = "auto" // Let the algorithm choose.
val impurity = "variance"
val maxDepth = 4
val maxBins = 32
val model = RandomForest.trainRegressor(trainingData, categoricalFeaturesInfo,
  numTrees, featureSubsetStrategy, impurity, maxDepth, maxBins)
// Evaluate model on test instances and compute test error
val labelsAndPredictions = testData.map { point =>
  val prediction = model.predict(point.features)
  (point.label, prediction)
}
val testMSE = labelsAndPredictions.map{ case(v, p) => math.pow((v - p), 2)}.mean()
println("Test Mean Squared Error = " + testMSE)
println("Learned regression forest model:\n" + model.toDebugString)
```

## 5 源码分析

### 5.1 训练分析

&emsp;&emsp;训练过程简单可以分为两步，第一步是初始化，第二步是迭代构建随机森林。这两大步还分为若干小步，下面会分别介绍这些内容。

#### 5.1.1 初始化

```scala
val retaggedInput = input.retag(classOf[LabeledPoint])
//建立决策树的元数据信息（分裂点位置、箱子数及各箱子包含特征属性的值等）
val metadata =
    DecisionTreeMetadata.buildMetadata(retaggedInput, strategy, numTrees, featureSubsetStrategy)
//找到切分点（splits）及箱子信息（Bins）
//对于连续型特征，利用切分点抽样统计简化计算
//对于离散型特征，如果是无序的，则最多有个 splits=2^(numBins-1)-1 划分
//如果是有序的，则最多有 splits=numBins-1 个划分
val (splits, bins) = DecisionTree.findSplitsBins(retaggedInput, metadata)
//转换成树形的 RDD 类型，转换后，所有样本点已经按分裂点条件分到了各自的箱子中
val treeInput = TreePoint.convertToTreeRDD(retaggedInput, bins, metadata)
val withReplacement = if (numTrees > 1) true else false
// convertToBaggedRDD 方法使得每棵树就是样本的一个子集
val baggedInput = BaggedPoint.convertToBaggedRDD(treeInput,
          strategy.subsamplingRate, numTrees,
          withReplacement, seed).persist(StorageLevel.MEMORY_AND_DISK)
//决策树的深度，最大为30
val maxDepth = strategy.maxDepth
//聚合的最大内存
val maxMemoryUsage: Long = strategy.maxMemoryInMB * 1024L * 1024L
val maxMemoryPerNode = {
    val featureSubset: Option[Array[Int]] = if (metadata.subsamplingFeatures) {
        // Find numFeaturesPerNode largest bins to get an upper bound on memory usage.
        Some(metadata.numBins.zipWithIndex.sortBy(- _._1)
          .take(metadata.numFeaturesPerNode).map(_._2))
    } else {
        None
    }
    //计算聚合操作时节点的内存
    RandomForest.aggregateSizeForNode(metadata, featureSubset) * 8L
}
```

&emsp;&emsp;初始化的第一步就是决策树元数据信息的构建。它的代码如下所示。

```scala
def buildMetadata(
      input: RDD[LabeledPoint],
      strategy: Strategy,
      numTrees: Int,
      featureSubsetStrategy: String): DecisionTreeMetadata = {
    //特征数
    val numFeatures = input.map(_.features.size).take(1).headOption.getOrElse {
      throw new IllegalArgumentException(s"DecisionTree requires size of input RDD > 0, " +
        s"but was given by empty one.")
    }
    val numExamples = input.count()
    val numClasses = strategy.algo match {
      case Classification => strategy.numClasses
      case Regression => 0
    }
    //最大可能的装箱数
    val maxPossibleBins = math.min(strategy.maxBins, numExamples).toInt
    if (maxPossibleBins < strategy.maxBins) {
      logWarning(s"DecisionTree reducing maxBins from ${strategy.maxBins} to $maxPossibleBins" +
        s" (= number of training instances)")
    }
    // We check the number of bins here against maxPossibleBins.
    // This needs to be checked here instead of in Strategy since maxPossibleBins can be modified
    // based on the number of training examples.
    //最大分类数要小于最大可能装箱数
    //这里categoricalFeaturesInfo是传入的信息，这个map保存特征的类别信息。
    //例如，(n->k)表示特征k包含的类别有（0,1,...,k-1）
    if (strategy.categoricalFeaturesInfo.nonEmpty) {
      val maxCategoriesPerFeature = strategy.categoricalFeaturesInfo.values.max
      val maxCategory =
        strategy.categoricalFeaturesInfo.find(_._2 == maxCategoriesPerFeature).get._1
      require(maxCategoriesPerFeature <= maxPossibleBins,
        s"DecisionTree requires maxBins (= $maxPossibleBins) to be at least as large as the " +
        s"number of values in each categorical feature, but categorical feature $maxCategory " +
        s"has $maxCategoriesPerFeature values. Considering remove this and other categorical " +
        "features with a large number of values, or add more training examples.")
    }
    val unorderedFeatures = new mutable.HashSet[Int]()
    val numBins = Array.fill[Int](numFeatures)(maxPossibleBins)
    if (numClasses > 2) {
      // 多分类
      val maxCategoriesForUnorderedFeature =
        ((math.log(maxPossibleBins / 2 + 1) / math.log(2.0)) + 1).floor.toInt
      strategy.categoricalFeaturesInfo.foreach { case (featureIndex, numCategories) =>
        //如果类别特征只有1个类，我们把它看成连续的特征
        if (numCategories > 1) {
          // Decide if some categorical features should be treated as unordered features,
          //  which require 2 * ((1 << numCategories - 1) - 1) bins.
          // We do this check with log values to prevent overflows in case numCategories is large.
          // The next check is equivalent to: 2 * ((1 << numCategories - 1) - 1) <= maxBins
          if (numCategories <= maxCategoriesForUnorderedFeature) {
            unorderedFeatures.add(featureIndex)
            numBins(featureIndex) = numUnorderedBins(numCategories)
          } else {
            numBins(featureIndex) = numCategories
          }
        }
      }
    } else {
      // 二分类或者回归
      strategy.categoricalFeaturesInfo.foreach { case (featureIndex, numCategories) =>
        //如果类别特征只有1个类，我们把它看成连续的特征
        if (numCategories > 1) {
          numBins(featureIndex) = numCategories
        }
      }
    }
    // 设置每个节点的特征数 (对随机森林而言).
    val _featureSubsetStrategy = featureSubsetStrategy match {
      case "auto" =>
        if (numTrees == 1) {//决策树时，使用所有特征
          "all"
        } else {
          if (strategy.algo == Classification) {//分类时，使用开平方
            "sqrt"
          } else { //回归时，使用1/3的特征
            "onethird"
          }
        }
      case _ => featureSubsetStrategy
    }
    val numFeaturesPerNode: Int = _featureSubsetStrategy match {
      case "all" => numFeatures
      case "sqrt" => math.sqrt(numFeatures).ceil.toInt
      case "log2" => math.max(1, (math.log(numFeatures) / math.log(2)).ceil.toInt)
      case "onethird" => (numFeatures / 3.0).ceil.toInt
    }
    new DecisionTreeMetadata(numFeatures, numExamples, numClasses, numBins.max,
      strategy.categoricalFeaturesInfo, unorderedFeatures.toSet, numBins,
      strategy.impurity, strategy.quantileCalculationStrategy, strategy.maxDepth,
      strategy.minInstancesPerNode, strategy.minInfoGain, numTrees, numFeaturesPerNode)
  }
```

&emsp;&emsp;初始化的第二步就是找到切分点（`splits`）及箱子信息（`Bins`）。这时，调用了`DecisionTree.findSplitsBins`方法，进入该方法了解详细信息。

```scala
/**
   * Returns splits and bins for decision tree calculation.
   * Continuous and categorical features are handled differently.
   *
   * Continuous features:
   *   For each feature, there are numBins - 1 possible splits representing the possible binary
   *   decisions at each node in the tree.
   *   This finds locations (feature values) for splits using a subsample of the data.
   *
   * Categorical features:
   *   For each feature, there is 1 bin per split.
   *   Splits and bins are handled in 2 ways:
   *   (a) "unordered features"
   *       For multiclass classification with a low-arity feature
   *       (i.e., if isMulticlass && isSpaceSufficientForAllCategoricalSplits),
   *       the feature is split based on subsets of categories.
   *   (b) "ordered features"
   *       For regression and binary classification,
   *       and for multiclass classification with a high-arity feature,
   *       there is one bin per category.
   *
   * @param input Training data: RDD of [[org.apache.spark.mllib.regression.LabeledPoint]]
   * @param metadata Learning and dataset metadata
   * @return A tuple of (splits, bins).
   *         Splits is an Array of [[org.apache.spark.mllib.tree.model.Split]]
   *          of size (numFeatures, numSplits).
   *         Bins is an Array of [[org.apache.spark.mllib.tree.model.Bin]]
   *          of size (numFeatures, numBins).
   */
  protected[tree] def findSplitsBins(
      input: RDD[LabeledPoint],
      metadata: DecisionTreeMetadata): (Array[Array[Split]], Array[Array[Bin]]) = {
    //特征数
    val numFeatures = metadata.numFeatures
    // Sample the input only if there are continuous features.
    // 判断特征中是否存在连续特征
    val continuousFeatures = Range(0, numFeatures).filter(metadata.isContinuous)
    val sampledInput = if (continuousFeatures.nonEmpty) {
      // Calculate the number of samples for approximate quantile calculation.
      //采样样本数量，最少有 10000 个
      val requiredSamples = math.max(metadata.maxBins * metadata.maxBins, 10000)
      //计算采样比例
      val fraction = if (requiredSamples < metadata.numExamples) {
        requiredSamples.toDouble / metadata.numExamples
      } else {
        1.0
      }
      //采样数据，有放回采样
      input.sample(withReplacement = false, fraction, new XORShiftRandom().nextInt())
    } else {
      input.sparkContext.emptyRDD[LabeledPoint]
    }
    //分裂点策略，目前 Spark 中只实现了一种策略：排序 Sort
    metadata.quantileStrategy match {
      case Sort =>
        findSplitsBinsBySorting(sampledInput, metadata, continuousFeatures)
      case MinMax =>
        throw new UnsupportedOperationException("minmax not supported yet.")
      case ApproxHist =>
        throw new UnsupportedOperationException("approximate histogram not supported yet.")
    }
  }
```
&emsp;&emsp;我们进入`findSplitsBinsBySorting`方法了解`Sort`分裂策略的实现。

```scala
private def findSplitsBinsBySorting(
      input: RDD[LabeledPoint],
      metadata: DecisionTreeMetadata,
      continuousFeatures: IndexedSeq[Int]): (Array[Array[Split]], Array[Array[Bin]]) = {
    def findSplits(
        featureIndex: Int,
        featureSamples: Iterable[Double]): (Int, (Array[Split], Array[Bin])) = {
      //每个特征分别对应一组切分点位置，这里splits是有序的
      val splits = {
        // findSplitsForContinuousFeature 返回连续特征的所有切分位置
        val featureSplits = findSplitsForContinuousFeature(
          featureSamples.toArray,
          metadata,
          featureIndex)
        featureSplits.map(threshold => new Split(featureIndex, threshold, Continuous, Nil))
      }
      //存放切分点位置对应的箱子信息
      val bins = {
        //采用最小阈值 Double.MinValue 作为最左边的分裂位置并进行装箱
        val lowSplit = new DummyLowSplit(featureIndex, Continuous)
        //最后一个箱子的计算采用最大阈值 Double.MaxValue 作为最右边的切分位置
        val highSplit = new DummyHighSplit(featureIndex, Continuous)
        // tack the dummy splits on either side of the computed splits
        val allSplits = lowSplit +: splits.toSeq :+ highSplit
        //将切分点两两结合成一个箱子
        allSplits.sliding(2).map {
          case Seq(left, right) => new Bin(left, right, Continuous, Double.MinValue)
        }.toArray
      }
      (featureIndex, (splits, bins))
    }
    val continuousSplits = {
      // reduce the parallelism for split computations when there are less
      // continuous features than input partitions. this prevents tasks from
      // being spun up that will definitely do no work.
      val numPartitions = math.min(continuousFeatures.length, input.partitions.length)
      input
        .flatMap(point => continuousFeatures.map(idx => (idx, point.features(idx))))
        .groupByKey(numPartitions)
        .map { case (k, v) => findSplits(k, v) }
        .collectAsMap()
    }
    val numFeatures = metadata.numFeatures
    //遍历所有特征
    val (splits, bins) = Range(0, numFeatures).unzip {
      //处理连续特征的情况
      case i if metadata.isContinuous(i) =>
        val (split, bin) = continuousSplits(i)
        metadata.setNumSplits(i, split.length)
        (split, bin)
      //处理离散特征且无序的情况
      case i if metadata.isCategorical(i) && metadata.isUnordered(i) =>
        // Unordered features
        // 2^(maxFeatureValue - 1) - 1 combinations
        val featureArity = metadata.featureArity(i)
        val split = Range(0, metadata.numSplits(i)).map { splitIndex =>
          val categories = extractMultiClassCategories(splitIndex + 1, featureArity)
          new Split(i, Double.MinValue, Categorical, categories)
        }
        // For unordered categorical features, there is no need to construct the bins.
        // since there is a one-to-one correspondence between the splits and the bins.
        (split.toArray, Array.empty[Bin])
      //处理离散特征且有序的情况
      case i if metadata.isCategorical(i) =>
        //有序特征无需处理，箱子与特征值对应
        // Ordered features
        // Bins correspond to feature values, so we do not need to compute splits or bins
        // beforehand.  Splits are constructed as needed during training.
        (Array.empty[Split], Array.empty[Bin])
    }
    (splits.toArray, bins.toArray)
  }
```

&emsp;&emsp;计算连续特征的所有切分位置需要调用方法`findSplitsForContinuousFeature`方法。

```scala
private[tree] def findSplitsForContinuousFeature(
      featureSamples: Array[Double],
      metadata: DecisionTreeMetadata,
      featureIndex: Int): Array[Double] = {
    val splits = {
      //切分数是bin的数量减1，即m-1
      val numSplits = metadata.numSplits(featureIndex)
      // （特征，特征出现的次数）
      val valueCountMap = featureSamples.foldLeft(Map.empty[Double, Int]) { (m, x) =>
        m + ((x, m.getOrElse(x, 0) + 1))
      }
      // 根据特征进行排序
      val valueCounts = valueCountMap.toSeq.sortBy(_._1).toArray
      // if possible splits is not enough or just enough, just return all possible splits
      val possibleSplits = valueCounts.length
      //如果特征数小于切分数，所有特征均作为切分点
      if (possibleSplits <= numSplits) {
        valueCounts.map(_._1)
      } else {
        // 等频切分
        // 切分点之间的步长
        val stride: Double = featureSamples.length.toDouble / (numSplits + 1)
        val splitsBuilder = Array.newBuilder[Double]
        var index = 1
        // currentCount: sum of counts of values that have been visited
        //第一个特征的出现次数
        var currentCount = valueCounts(0)._2
        // targetCount: target value for `currentCount`.
        // If `currentCount` is closest value to `targetCount`,
        // then current value is a split threshold.
        // After finding a split threshold, `targetCount` is added by stride.
        // 如果currentCount离targetCount最近，那么当前值是切分点
        var targetCount = stride
        while (index < valueCounts.length) {
          val previousCount = currentCount
          currentCount += valueCounts(index)._2
          val previousGap = math.abs(previousCount - targetCount)
          val currentGap = math.abs(currentCount - targetCount)
          // If adding count of current value to currentCount
          // makes the gap between currentCount and targetCount smaller,
          // previous value is a split threshold.
          if (previousGap < currentGap) {
            splitsBuilder += valueCounts(index - 1)._1
            targetCount += stride
          }
          index += 1
        }
        splitsBuilder.result()
      }
    }
    splits
  }
```
&emsp;&emsp; 在if判断里每步前进`stride`个样本，累加在`targetCount`中。`while`循环逐次把每个特征值的个数加到`currentCount`里，计算前一次`previousCount`和这次`currentCount`到`targetCount`的距离，有3种情况，一种是`pre`和`cur`都在`target`左边，肯定是`cur`小，继续循环，进入第二种情况；第二种一左一右，如果`pre`小，肯定是`pre`是最好的分割点，如果`cur`还是小，继续循环步进，进入第三种情况；第三种就是都在右边，显然是`pre`小。因此`if`的判断条件`pre<cur`，只要满足肯定就是`split`。整体下来的效果就能找到离`target`最近的一个特征值。

#### 5.1.2 迭代构建随机森林

```scala
//节点是否使用缓存，节点 ID 从 1 开始，1 即为这颗树的根节点，左节点为 2，右节点为 3，依次递增下去
val nodeIdCache = if (strategy.useNodeIdCache) {
   Some(NodeIdCache.init(
        data = baggedInput,
        numTrees = numTrees,
        checkpointInterval = strategy.checkpointInterval,
        initVal = 1))
} else {
   None
}
// FIFO queue of nodes to train: (treeIndex, node)
val nodeQueue = new mutable.Queue[(Int, Node)]()
val rng = new scala.util.Random()
rng.setSeed(seed)
// Allocate and queue root nodes.
//创建树的根节点
val topNodes: Array[Node] = Array.fill[Node](numTrees)(Node.emptyNode(nodeIndex = 1))
//将（树的索引，树的根节点）入队，树索引从 0 开始，根节点从 1 开始
Range(0, numTrees).foreach(treeIndex => nodeQueue.enqueue((treeIndex, topNodes(treeIndex))))
while (nodeQueue.nonEmpty) {
    // Collect some nodes to split, and choose features for each node (if subsampling).
    // Each group of nodes may come from one or multiple trees, and at multiple levels.
    // 取得每个树所有需要切分的节点,nodesForGroup表示需要切分的节点
    val (nodesForGroup, treeToNodeToIndexInfo) =
        RandomForest.selectNodesToSplit(nodeQueue, maxMemoryUsage, metadata, rng)
    //找出最优切点
    DecisionTree.findBestSplits(baggedInput, metadata, topNodes, nodesForGroup,
        treeToNodeToIndexInfo, splits, bins, nodeQueue, timer, nodeIdCache = nodeIdCache)
}
```

&emsp;&emsp;这里有两点需要重点介绍，第一点是取得每个树所有需要切分的节点，通过`RandomForest.selectNodesToSplit`方法实现；第二点是找出最优的切分，通过`DecisionTree.findBestSplits`方法实现。下面分别介绍这两点。

- 取得每个树所有需要切分的节点

```scala
 private[tree] def selectNodesToSplit(
      nodeQueue: mutable.Queue[(Int, Node)],
      maxMemoryUsage: Long,
      metadata: DecisionTreeMetadata,
      rng: scala.util.Random): (Map[Int, Array[Node]], Map[Int, Map[Int, NodeIndexInfo]]) = {
    // nodesForGroup保存需要切分的节点，treeIndex --> nodes
    val mutableNodesForGroup = new mutable.HashMap[Int, mutable.ArrayBuffer[Node]]()
    // mutableTreeToNodeToIndexInfo保存每个节点中选中特征的索引
    // treeIndex --> (global) node index --> (node index in group, feature indices)
    //(global) node index是树中的索引，组中节点索引的范围是[0, numNodesInGroup)
    val mutableTreeToNodeToIndexInfo =
      new mutable.HashMap[Int, mutable.HashMap[Int, NodeIndexInfo]]()
    var memUsage: Long = 0L
    var numNodesInGroup = 0
    while (nodeQueue.nonEmpty && memUsage < maxMemoryUsage) {
      val (treeIndex, node) = nodeQueue.head
      // Choose subset of features for node (if subsampling).
      // 选中特征子集
      val featureSubset: Option[Array[Int]] = if (metadata.subsamplingFeatures) {
        Some(SamplingUtils.reservoirSampleAndCount(Range(0,
          metadata.numFeatures).iterator, metadata.numFeaturesPerNode, rng.nextLong)._1)
      } else {
        None
      }
      // Check if enough memory remains to add this node to the group.
      // 检查是否有足够的内存
      val nodeMemUsage = RandomForest.aggregateSizeForNode(metadata, featureSubset) * 8L
      if (memUsage + nodeMemUsage <= maxMemoryUsage) {
        nodeQueue.dequeue()
        mutableNodesForGroup.getOrElseUpdate(treeIndex, new mutable.ArrayBuffer[Node]()) += node
        mutableTreeToNodeToIndexInfo
          .getOrElseUpdate(treeIndex, new mutable.HashMap[Int, NodeIndexInfo]())(node.id)
          = new NodeIndexInfo(numNodesInGroup, featureSubset)
      }
      numNodesInGroup += 1
      memUsage += nodeMemUsage
    }
    // 将可变map转换为不可变map
    val nodesForGroup: Map[Int, Array[Node]] = mutableNodesForGroup.mapValues(_.toArray).toMap
    val treeToNodeToIndexInfo = mutableTreeToNodeToIndexInfo.mapValues(_.toMap).toMap
    (nodesForGroup, treeToNodeToIndexInfo)
  }
```

- 选中最优切分

```scala
//所有可切分的节点
val nodes = new Array[Node](numNodes)
nodesForGroup.foreach { case (treeIndex, nodesForTree) =>
   nodesForTree.foreach { node =>
     nodes(treeToNodeToIndexInfo(treeIndex)(node.id).nodeIndexInGroup) = node
   }
}
// In each partition, iterate all instances and compute aggregate stats for each node,
// yield an (nodeIndex, nodeAggregateStats) pair for each node.
// After a `reduceByKey` operation,
// stats of a node will be shuffled to a particular partition and be combined together,
// then best splits for nodes are found there.
// Finally, only best Splits for nodes are collected to driver to construct decision tree.
//获取节点对应的特征
val nodeToFeatures = getNodeToFeatures(treeToNodeToIndexInfo)
val nodeToFeaturesBc = input.sparkContext.broadcast(nodeToFeatures)
val partitionAggregates : RDD[(Int, DTStatsAggregator)] = if (nodeIdCache.nonEmpty) {
    input.zip(nodeIdCache.get.nodeIdsForInstances).mapPartitions { points =>
      // Construct a nodeStatsAggregators array to hold node aggregate stats,
      // each node will have a nodeStatsAggregator
      val nodeStatsAggregators = Array.tabulate(numNodes) { nodeIndex =>
          //节点对应的特征集
          val featuresForNode = nodeToFeaturesBc.value.flatMap { nodeToFeatures =>
            Some(nodeToFeatures(nodeIndex))
          }
          // DTStatsAggregator，其中引用了 ImpurityAggregator，给出计算不纯度 impurity 的逻辑
          new DTStatsAggregator(metadata, featuresForNode)
      }
      // 迭代当前分区的所有对象，更新聚合统计信息，统计信息即采样数据的权重值
      points.foreach(binSeqOpWithNodeIdCache(nodeStatsAggregators, _))
      // transform nodeStatsAggregators array to (nodeIndex, nodeAggregateStats) pairs,
      // which can be combined with other partition using `reduceByKey`
      nodeStatsAggregators.view.zipWithIndex.map(_.swap).iterator
    }
} else {
      input.mapPartitions { points =>
        // Construct a nodeStatsAggregators array to hold node aggregate stats,
        // each node will have a nodeStatsAggregator
        val nodeStatsAggregators = Array.tabulate(numNodes) { nodeIndex =>
          //节点对应的特征集
          val featuresForNode = nodeToFeaturesBc.value.flatMap { nodeToFeatures =>
            Some(nodeToFeatures(nodeIndex))
          }
          // DTStatsAggregator，其中引用了 ImpurityAggregator，给出计算不纯度 impurity 的逻辑
          new DTStatsAggregator(metadata, featuresForNode)
        }
        // 迭代当前分区的所有对象，更新聚合统计信息
        points.foreach(binSeqOp(nodeStatsAggregators, _))
        // transform nodeStatsAggregators array to (nodeIndex, nodeAggregateStats) pairs,
        // which can be combined with other partition using `reduceByKey`
        nodeStatsAggregators.view.zipWithIndex.map(_.swap).iterator
      }
}
val nodeToBestSplits = partitionAggregates.reduceByKey((a, b) => a.merge(b))
    .map { case (nodeIndex, aggStats) =>
          val featuresForNode = nodeToFeaturesBc.value.map { nodeToFeatures =>
            nodeToFeatures(nodeIndex)
    }
    // find best split for each node
    val (split: Split, stats: InformationGainStats, predict: Predict) =
        binsToBestSplit(aggStats, splits, featuresForNode, nodes(nodeIndex))
    (nodeIndex, (split, stats, predict))
}.collectAsMap()
```

&emsp;&emsp;该方法中的关键是对`binsToBestSplit`方法的调用，`binsToBestSplit`方法代码如下：

```scala
private def binsToBestSplit(
      binAggregates: DTStatsAggregator,
      splits: Array[Array[Split]],
      featuresForNode: Option[Array[Int]],
      node: Node): (Split, InformationGainStats, Predict) = {
    // 如果当前节点是根节点，计算预测和不纯度
    val level = Node.indexToLevel(node.id)
    var predictWithImpurity: Option[(Predict, Double)] = if (level == 0) {
      None
    } else {
      Some((node.predict, node.impurity))
    }
    // 对各特征及切分点，计算其信息增益并从中选择最优 (feature, split)
    val (bestSplit, bestSplitStats) =
      Range(0, binAggregates.metadata.numFeaturesPerNode).map { featureIndexIdx =>
      val featureIndex = if (featuresForNode.nonEmpty) {
        featuresForNode.get.apply(featureIndexIdx)
      } else {
        featureIndexIdx
      }
      val numSplits = binAggregates.metadata.numSplits(featureIndex)
       //特征为连续值的情况
      if (binAggregates.metadata.isContinuous(featureIndex)) {
        // Cumulative sum (scanLeft) of bin statistics.
        // Afterwards, binAggregates for a bin is the sum of aggregates for
        // that bin + all preceding bins.
        val nodeFeatureOffset = binAggregates.getFeatureOffset(featureIndexIdx)
        var splitIndex = 0
        while (splitIndex < numSplits) {
          binAggregates.mergeForFeature(nodeFeatureOffset, splitIndex + 1, splitIndex)
          splitIndex += 1
        }
        // Find best split.
        val (bestFeatureSplitIndex, bestFeatureGainStats) =
          Range(0, numSplits).map { case splitIdx =>
            //计算 leftChild 及 rightChild 子节点的 impurity
            val leftChildStats = binAggregates.getImpurityCalculator(nodeFeatureOffset, splitIdx)
            val rightChildStats = binAggregates.getImpurityCalculator(nodeFeatureOffset, numSplits)
            rightChildStats.subtract(leftChildStats)
            //求 impurity 的预测值，采用的是平均值计算
            predictWithImpurity = Some(predictWithImpurity.getOrElse(
              calculatePredictImpurity(leftChildStats, rightChildStats)))
            //求信息增益 information gain 值，用于评估切分点是否最优,请参考决策树中1.4.4章节的介绍
            val gainStats = calculateGainForSplit(leftChildStats,
              rightChildStats, binAggregates.metadata, predictWithImpurity.get._2)
            (splitIdx, gainStats)
          }.maxBy(_._2.gain)
        (splits(featureIndex)(bestFeatureSplitIndex), bestFeatureGainStats)
      }
      //无序离散特征时的情况
      else if (binAggregates.metadata.isUnordered(featureIndex)) {
        // Unordered categorical feature
        val (leftChildOffset, rightChildOffset) =
          binAggregates.getLeftRightFeatureOffsets(featureIndexIdx)
        val (bestFeatureSplitIndex, bestFeatureGainStats) =
          Range(0, numSplits).map { splitIndex =>
            val leftChildStats = binAggregates.getImpurityCalculator(leftChildOffset, splitIndex)
            val rightChildStats = binAggregates.getImpurityCalculator(rightChildOffset, splitIndex)
            predictWithImpurity = Some(predictWithImpurity.getOrElse(
              calculatePredictImpurity(leftChildStats, rightChildStats)))
            val gainStats = calculateGainForSplit(leftChildStats,
              rightChildStats, binAggregates.metadata, predictWithImpurity.get._2)
            (splitIndex, gainStats)
          }.maxBy(_._2.gain)
        (splits(featureIndex)(bestFeatureSplitIndex), bestFeatureGainStats)
      } else {//有序离散特征时的情况
        // Ordered categorical feature
        val nodeFeatureOffset = binAggregates.getFeatureOffset(featureIndexIdx)
        val numBins = binAggregates.metadata.numBins(featureIndex)
        /* Each bin is one category (feature value).
         * The bins are ordered based on centroidForCategories, and this ordering determines which
         * splits are considered.  (With K categories, we consider K - 1 possible splits.)
         *
         * centroidForCategories is a list: (category, centroid)
         */
        //多元分类时的情况
        val centroidForCategories = if (binAggregates.metadata.isMulticlass) {
          // For categorical variables in multiclass classification,
          // the bins are ordered by the impurity of their corresponding labels.
          Range(0, numBins).map { case featureValue =>
            val categoryStats = binAggregates.getImpurityCalculator(nodeFeatureOffset, featureValue)
            val centroid = if (categoryStats.count != 0) {
              // impurity 求的就是均方差
              categoryStats.calculate()
            } else {
              Double.MaxValue
            }
            (featureValue, centroid)
          }
        } else { // 回归或二元分类时的情况
          // For categorical variables in regression and binary classification,
          // the bins are ordered by the centroid of their corresponding labels.
          Range(0, numBins).map { case featureValue =>
            val categoryStats = binAggregates.getImpurityCalculator(nodeFeatureOffset, featureValue)
            val centroid = if (categoryStats.count != 0) {
              //求的就是平均值作为 impurity
              categoryStats.predict
            } else {
              Double.MaxValue
            }
            (featureValue, centroid)
          }
        }
        // bins sorted by centroids
        val categoriesSortedByCentroid = centroidForCategories.toList.sortBy(_._2)
        // Cumulative sum (scanLeft) of bin statistics.
        // Afterwards, binAggregates for a bin is the sum of aggregates for
        // that bin + all preceding bins.
        var splitIndex = 0
        while (splitIndex < numSplits) {
          val currentCategory = categoriesSortedByCentroid(splitIndex)._1
          val nextCategory = categoriesSortedByCentroid(splitIndex + 1)._1
          //将两个箱子的状态信息进行合并
          binAggregates.mergeForFeature(nodeFeatureOffset, nextCategory, currentCategory)
          splitIndex += 1
        }
        // lastCategory = index of bin with total aggregates for this (node, feature)
        val lastCategory = categoriesSortedByCentroid.last._1
        // Find best split.
        //通过信息增益值选择最优切分点
        val (bestFeatureSplitIndex, bestFeatureGainStats) =
          Range(0, numSplits).map { splitIndex =>
            val featureValue = categoriesSortedByCentroid(splitIndex)._1
            val leftChildStats =
              binAggregates.getImpurityCalculator(nodeFeatureOffset, featureValue)
            val rightChildStats =
              binAggregates.getImpurityCalculator(nodeFeatureOffset, lastCategory)
            rightChildStats.subtract(leftChildStats)
            predictWithImpurity = Some(predictWithImpurity.getOrElse(
              calculatePredictImpurity(leftChildStats, rightChildStats)))
            val gainStats = calculateGainForSplit(leftChildStats,
              rightChildStats, binAggregates.metadata, predictWithImpurity.get._2)
            (splitIndex, gainStats)
          }.maxBy(_._2.gain)
        val categoriesForSplit =
          categoriesSortedByCentroid.map(_._1.toDouble).slice(0, bestFeatureSplitIndex + 1)
        val bestFeatureSplit =
          new Split(featureIndex, Double.MinValue, Categorical, categoriesForSplit)
        (bestFeatureSplit, bestFeatureGainStats)
      }
    }.maxBy(_._2.gain)
    (bestSplit, bestSplitStats, predictWithImpurity.get._1)
  }
```

### 5.2 预测分析

&emsp;&emsp;在利用随机森林进行预测时，调用的`predict`方法扩展自`TreeEnsembleModel`，它是树结构组合模型的表示，其核心代码如下所示：

```scala
//不同的策略采用不同的预测方法
def predict(features: Vector): Double = {
    (algo, combiningStrategy) match {
      case (Regression, Sum) =>
        predictBySumming(features)
      case (Regression, Average) =>
        predictBySumming(features) / sumWeights
      case (Classification, Sum) => // binary classification
        val prediction = predictBySumming(features)
        // TODO: predicted labels are +1 or -1 for GBT. Need a better way to store this info.
        if (prediction > 0.0) 1.0 else 0.0
      case (Classification, Vote) =>
        predictByVoting(features)
      case _ =>
        throw new IllegalArgumentException()
    }
  }
private def predictBySumming(features: Vector): Double = {
    val treePredictions = trees.map(_.predict(features))
    //两个向量的内集
    blas.ddot(numTrees, treePredictions, 1, treeWeights, 1)
}
//通过投票选举
private def predictByVoting(features: Vector): Double = {
    val votes = mutable.Map.empty[Int, Double]
    trees.view.zip(treeWeights).foreach { case (tree, weight) =>
      val prediction = tree.predict(features).toInt
      votes(prediction) = votes.getOrElse(prediction, 0.0) + weight
    }
    votes.maxBy(_._2)._1
}
```

# 参考文献

【1】机器学习.周志华

【2】[Spark 随机森林算法原理、源码分析及案例实战](https://www.ibm.com/developerworks/cn/opensource/os-cn-spark-random-forest/)

【3】[Scalable Distributed Decision Trees in Spark MLlib](https://spark-summit.org/wp-content/uploads/2014/07/Scalable-Distributed-Decision-Trees-in-Spark-Made-Das-Sparks-Talwalkar.pdf)


# 生存回归

## 1 基本概念

### 1.1 生存数据

&emsp;&emsp;生存数据就是关于某个体生存时间的数据。生存时间就是死亡时间减去出生时间。例如，以一个自然人的出生为“出生”，死亡为“死亡”。
那么，死亡时间减去出生时间，就是一个人的寿命，这是一个典型的生存数据。类似的例子，还可以举出很多。所有这些数据都有一个共同的特点，
就是需要清晰定义的：出生和死亡 。如果用死亡时间减去出生时间，就产生了一个生存数据。因为死亡一定发生在出生的后面，因此，生存数据一定是正数。
因为，从理论上讲，出生死亡时间都可能取任意数值，因此 生存数据一定是连续的正数。

&emsp;&emsp;生存期不同于一般指标，他有二个特点：

- 1 有截尾数据（`censored data`)

&emsp;&emsp;例如我们在疾病预测的实验中，随访未能知道病人的确切生存时间，只知道病人的生存时间大于某时间。

（1）病人失访或因其他原因而死亡---失访
（2）到了研究的终止期病人尚未死亡---终访

&emsp;&emsp;例如，一个人的寿命。假设我关心`1949`年出生的人群的平均寿命。这群人可以被分成两部分。一部分是已经离世了，所以他们的死亡时间是准确知道的。因此，他们的寿命是非常清晰的。
另一部分，是所有健在的人群，他们从`1949`年出生到现在，已经走过了将近70个春秋岁月，但是他们还活着！到`2017`年为止，他们已经生存了`68`年，但是他们最终的寿命是多少？我们是不知道的。
我们知道他们的寿命一定会比`68`大，数学上可以被记作`68+`。但是，到底“+”多少，不清楚。

&emsp;&emsp;虽然截尾数据提供的信息是不完全的，但不能删去，因为这不仅损失了资料，而且会造成偏性。

- 2  生存时间的特征一般不服从正态分布

&emsp;&emsp;跟所有的数据分析一样，要分析生存数据，首要问题是做描述性分析。如果生存数据没有被截断，那么所有常规的描述统计量，估计量都适用。例如：样本均值，样本方差等。
但是，如果生存数据存在大量的截断数据，那么任何同均值相关的统计量就都没法计算了。例如：样本均值无法算，样本方差涉及到因变量的平方的均值，因此它也没法计算。

&emsp;&emsp;真实的数据常常非常复杂，每个样本的出生日期不同，死亡日期不同，截断时间点不同。但是，不管这个数据如何复杂，其背后的基本原理是一样的。
那就是：虽然样本均值没法估计，样本方差没法估计。但是，各种分位数却在一个很大的范围内可以被估计。如果这个范围大到可以覆盖中位数，那么从某种意义上讲，我们也就把握了生存的平均状况了。

&emsp;&emsp;总结一下就是：对生存数据最基本的描述分析方法，不是过去常见的样本均值，样本方差等等，而是各种分位数。这些分位数也就构成了所谓的生存函数。生存函数就变成了对生存数据最基本的描述统计。

### 1.2 描述生存时间分布规律的函数

- 1 生存率(`Survival Rate`)

&emsp;&emsp;又称为生存概率或生存函数，它表示生存时间长于时间`t`的概率，用`S(t)` 表示：`s(t)=P（T≥t）`。以时间`t`为横坐标，`S(t)`为纵坐标所作的曲线称为生存率曲线，它是一条下降的曲线，下降的坡度越陡，
表示生存率越低或生存时间越短，其斜率表示死亡速率。

- 2 概率密度函数(`Probability Density Function`)

&emsp;&emsp;其定义为:`f(t)=lim (一个病人在区间(t,t+△t)内死亡概率/△t)`，它表示死亡速率的大小。如以`t`为横坐，`f(t)`为纵坐标作出的曲线称为密度曲线，由曲线上可看出不同时间的死亡速率及死亡高峰时间。
纵坐标越大，其死亡速率越高，如曲线呈现单调下降，则死亡速率越来越小，如呈现峰值，则为死亡高峰。

- 3 风险函数(`Hazard Function`)

&emsp;&emsp;其定义为:`h(t)=lim(在时间t生存的病人死于区间(t,△t)的概率/△t)`，由于计算`h(t)`时，用到了生存到时间`t`这一条件，故上式极限式中分子部分是一个条件概率。
可将`h(t)`称为生存到时间`t`的病人在时间`t`的瞬时死亡率或条件死亡速率或年龄别死亡速率。当用`t`作横坐标，`h(t)`为纵坐标所绘的曲线，如递增，则表示条件死亡速率随时间而增加，如平行于横轴，
则表示没有随时间而加速(或减少)死亡的情况。

## 2 加速失效时间模型(AFT)

&emsp;&emsp;在生存分析领域，加速失效时间模型(`accelerated failure time model`,`AFT` 模型)可以作为比例风险模型的替代模型。`AFT`模型将线性回归模型的建模方法引人到生存分析的领域，
将生存时间的对数作为反应变量，研究多协变量与对数生存时间之间的回归关系，在形式上，模型与一般的线性回归模型相似。对回归系数的解释也与一般的线性回归模型相似，较之`Cox`模型，
`AFT`模型对分析结果的解释更加简单、直观且易于理解，并且可以预测个体的生存时间。

&emsp;&emsp;在`spark ml`中，实现了`AFT` 模型，这是一个用于检查数据的参数生存回归模型。它描述了生存时间对数的模型，因此它通常被称为生存分析的对数线性模型。不同于为相同目的设计的比例风险模型(`Proportional hazards model`)，
`AFT`模型更容易并行化，因为每个实例独立地贡献于目标函数。

&emsp;&emsp;给定给定协变量的值$x^{'}$，对于`i = 1, …, n`可能的右截尾的随机生存时间$t_{i}$，`AFT`模型的似然函数如下：

$$L(\beta,\sigma)=\prod_{i=1}^n[\frac{1}{\sigma}f_{0}(\frac{\log{t_{i}}-x^{'}\beta}{\sigma})]^{\delta_{i}}S_{0}(\frac{\log{t_{i}}-x^{'}\beta}{\sigma})^{1-\delta_{i}}$$

&emsp;&emsp;其中，$\delta_{i}$是指示器，它表示事件`i`是否发生了，即有无截尾。使$\epsilon_{i}=\frac{\log{t_{i}}-x^{‘}\beta}{\sigma}$，则对数似然函数为以下形式：

$$\iota(\beta,\sigma)=\sum_{i=1}^{n}[-\delta_{i}\log\sigma+\delta_{i}\log{f_{0}}(\epsilon_{i})+(1-\delta_{i})\log{S_{0}(\epsilon_{i})}]$$

&emsp;&emsp;其中$S_{0}(\epsilon_{i})$是基准生存函数，$f_{0}(\epsilon_{i})$是对应的概率密度函数。

&emsp;&emsp;最常用的`AFT`模型基于服从韦伯分布的生存时间，生存时间的韦伯分布对应于生存时间对数的极值分布，所以$S_{0}(\epsilon)$函数为：

$$S_{0}(\epsilon_{i})=\exp(-e^{\epsilon_{i}})$$

&emsp;&emsp;$f_{0}(\epsilon_{i})$函数为：

$$f_{0}(\epsilon_{i})=e^{\epsilon_{i}}\exp(-e^{\epsilon_{i}})$$

&emsp;&emsp;生存时间服从韦伯分布的`AFT`模型的对数似然函数如下：

$$\iota(\beta,\sigma)= -\sum_{i=1}^n[\delta_{i}\log\sigma-\delta_{i}\epsilon_{i}+e^{\epsilon_{i}}]$$

&emsp;&emsp;由于最小化对数似然函数的负数等于最大化后验概率，所以我们要优化的损失函数为$-\iota(\beta,\sigma)$。分别对$\beta$和$\log\sigma$求导，得到：

$$\frac{\partial (-\iota)}{\partial \beta}=\sum_{1=1}^{n}[\delta_{i}-e^{\epsilon_{i}}]\frac{x_{i}}{\sigma}$$

$$\frac{\partial (-\iota)}{\partial (\log\sigma)}=\sum_{i=1}^{n}[\delta_{i}+(\delta_{i}-e^{\epsilon_{i}})\epsilon_{i}]$$

&emsp;&emsp;可以证明`AFT`模型是一个凸优化问题，即是说找到凸函数$-\iota(\beta,\sigma)$的最小值取决于系数向量$\beta$以及尺度参数的对数$\log\sigma$。
`spark ml`中使用`L-BFGS`作为优化算法。

>>> 注意：当使用无拦截(`intercept`)的连续非零列训练`AFTSurvivalRegressionModel`时，`Spark MLlib`为连续非零列输出零系数。这种处理与R中的生存函数`survreg`不同。

## 3 例子

```scala
 val dataList: List[(Double, Double, Double, Double)] = List(
      (2, 51, 1, 1),
      (2, 58, 1, 1),
      (2, 55, 2, 1),
      (2, 28, 22, 1),
      (1, 21, 30, 0),
      (1, 19, 28, 1),
      (2, 25, 32, 1),
      (2, 48, 11, 1),
      (2, 47, 14, 1),
      (2, 25, 36, 0),
      (2, 31, 31, 0),
      (1, 24, 33, 0),
      (1, 25, 33, 0),
      (2, 30, 37, 0),
      (2, 33, 35, 0),
      (1, 36, 25, 1),
      (1, 30, 31, 0),
      (1, 41, 22, 1),
      (2, 43, 26, 1),
      (2, 45, 24, 1),
      (2, 35, 35, 0),
      (1, 29, 34, 0),
      (1, 35, 30, 0),
      (1, 32, 35, 1),
      (2, 36, 40, 1),
      (1, 32, 39, 0))
    val data = dataList.toDF("sex", "age", "label", "censor").orderBy("label")
    val colArray = Array("sex", "age")
    val assembler = new VectorAssembler().setInputCols(colArray).setOutputCol("features")
    val vecDF: DataFrame = assembler.transform(data)
    val aft = new AFTSurvivalRegression()
    val model = aft.fit(vecDF)
    // Print the coefficients, intercept and scale parameter for AFT survival regression
    println(s"Coefficients: ${model.coefficients} Intercept: " +
      s"${model.intercept} Scale: ${model.scale}")
    val Array(coeff1, coeff2) = model.coefficients.toArray
    val intercept: Double = model.intercept
    val scale: Double = model.scale
    val aftDF = model.transform(vecDF)
    // 风险率h(t)
    aftDF.selectExpr("sex", "age", "label", "censor",
      "features", "round(prediction,2) as prediction",
      s"round( exp( sex*$coeff1+age*$coeff2+$intercept ), 2) as h(t)").orderBy("label").show(100, false)
```

## 4 参考文献

【1】[Spark Doc](https://spark.apache.org/docs/latest/ml-classification-regression.html#survival-regression)

【2】[回归XY | 数据江湖：回归五式之第五式（生存回归）](https://www.wxzhi.com/archives/871/pj2zikqb49cof749/)


# 保序回归

## 1 保序回归

&emsp;&emsp;保序回归解决了下面的问题：给定包含`n`个数据点的序列 `y_1,y_2,...,y_n` , 怎样通过一个单调的序列 `beta_1,beta_2,...,beta_n` 来归纳这个问题。形式上，这个问题就是为了找到

<div  align="center"><img src="tech/ml/isotonic/imgs/1.1.png" width = "700" height = "70" alt="1.1" align="center" /></div><br>

&emsp;&emsp;大部分时候，我们会在括号前加上权重`w_i`。解决这个问题的一个方法就是 `pool adjacent violators algorithm(PAVA)` 算法。粗略的讲，`PAVA`算法的过程描述如下：

&emsp;&emsp;我们从左边的`y_1`开始，右移`y_1`直到我们遇到第一个违例(`violation`)即`y_i < y_i+1`，然后，我们用违例之前的所有`y`的平方替换这些`y`，以满足单调性。我们继续这个过程，直到我们最后到达`y_n`。

## 2 近似保序

&emsp;&emsp;给定一个序列`y_1,y_2,...,y_n`，我们寻找一个近似单调估计，考虑下面的问题

<div  align="center"><img src="tech/ml/isotonic/imgs/1.2.png" width = "700" height = "70" alt="1.2" align="center" /></div><br>

&emsp;&emsp;在上式中，`X_+`表示正数部分，即`X_+ = X.1 (x>0)`。这是一个凸优化问题，处罚项处罚违反单调性（即`beta_i > beta_i+1`）的邻近对。

&emsp;&emsp;在公式（2）中，隐含着一个假设，即使用等距的网格测量数据点。如果情况不是这样，那么可以修改惩罚项为下面的形式

<div  align="center"><img src="tech/ml/isotonic/imgs/1.3.png" width = "200" height = "75" alt="1.3" align="center" /></div><br>

&emsp;&emsp;`x_i`表示`y_i`测量得到的值。

## 3 近似保序算法流程

&emsp;&emsp;这个算法是标准`PAVA`算法的修改版本，它并不从数据的左端开始，而是在需要时连接相邻的点，以产生近似保序最优的顺序。相比一下，`PAVA`对中间的序列并不特别感兴趣，只关心最后的序列。

&emsp;&emsp;有下面一个引理成立。

<div  align="center"><img src="tech/ml/isotonic/imgs/1.4.png" width = "900" height = "60" alt="1.4" align="center" /></div><br>

&emsp;&emsp;这个引理证明的事实极大地简化了近似保序解路径（`solution path`）的构造。假设在参数值为`lambda`的情况下，有`K_lambda`个连接块，我们用`A_1,A_2,..,A_K_lambda`表示。这样我们可以重写（2）为如下（3）的形式。

<div  align="center"><img src="tech/ml/isotonic/imgs/1.5.png" width = "700" height = "85" alt="1.5" align="center" /></div><br>

&emsp;&emsp;上面的公式，对`beta`求偏导，可以得到下面的次梯度公式。通过这个公式即可以求得`beta`。

<div  align="center"><img src="tech/ml/isotonic/imgs/1.6.png" width = "800" height = "65" alt="1.6" align="center" /></div><br>

&emsp;&emsp;为了符合方便，令`s_0 = s_K_lambda = 0`。并且，

<div  align="center"><img src="tech/ml/isotonic/imgs/1.7.png" width = "300" height = "40" alt="1.7" align="center" /></div><br>

&emsp;&emsp;现在假设，当`lambda`在一个区间内增长时，组`A_1,A_2,...,A_K_lambda`不会改变。我们可以通过相应的`lambda`区分（4）。

<div  align="center"><img src="tech/ml/isotonic/imgs/1.8.png" width = "600" height = "65" alt="1.8" align="center" /></div><br>

&emsp;&emsp;这个公式的值本身是一个常量，它意味着上式的`beta`是`lambda`的线性函数。

&emsp;&emsp;随着`lambda`的增长，方程（5）将连续的给出解决方案的斜率直到组`A_1,A_2,...,A_K_lambda`改变。更加引理1，只有两个组合并时，这才会发生。`m_i`表示斜率，那么对于每一个`i=1,...,K_lambda - 1`，`A_i`和`A_i+1`合并之后得到的公式如下

<div  align="center"><img src="tech/ml/isotonic/imgs/1.9.png" width = "600" height = "75" alt="1.9" align="center" /></div><br>

&emsp;&emsp;因此我们可以一直移动，直到`lambda` “下一个”值的到来

<div  align="center"><img src="tech/ml/isotonic/imgs/1.10.png" width = "600" height = "50" alt="1.10" align="center" /></div><br>

&emsp;&emsp;并且合并`A_i^star`和`A_i^star+1`,其中

<div  align="center"><img src="tech/ml/isotonic/imgs/1.11.png" width = "600" height = "55" alt="1.11" align="center" /></div><br>

&emsp;&emsp;注意，可能有超过一对组别到达了这个最小值，在这种情况下，会组合所有满足条件的组别。公式（7）和（8）成立的条件是`t_i,i+1`大于`lambda`，如果没有`t_i,i+1`大于`lambda`，说明没有组别可以合并，算法将会终止。

&emsp;&emsp;**算法的流程如下**：

- 初始时，`lambda=0`，`K_lambda=n`,`A_i={i},i=1,2,...,n`。对于每个i，解是`beta_lambda,i = y_i`

- 重复下面过程

&emsp;&emsp;**1、**通过公式（5）计算每个组的斜率`m_i`

&emsp;&emsp;**2、**通过公式（6）计算没对相邻组的碰撞次数`t_i,i+1`

&emsp;&emsp;**3、**如果`t_i,i+1 < lambda`，终止

&emsp;&emsp;**4、**计算公式（7）中的临界点`lambda^star`,并根据斜率更新解

<div  align="center"><img src="tech/ml/isotonic/imgs/1.12.png" width = "350" height = "45" alt="1.12" align="center" /></div><br>

&emsp;&emsp;对于每个`i`，根据公式（8）合并合适的组别（所以`K_lambda^star = K_lambda - 1`），并设置`lambda = lambda^star`。

## 4 源码分析

&emsp;&emsp;在`1.6.x`版本中，并没有实现近似保序回归，后续会实现。现在我们只介绍一般的保序回归算法实现。

### 4.1 实例

```scala
import org.apache.spark.mllib.regression.{IsotonicRegression, IsotonicRegressionModel}
val data = sc.textFile("data/mllib/sample_isotonic_regression_data.txt")
// 创建（label, feature, weight） tuples ，权重默认设置为1.0
val parsedData = data.map { line =>
  val parts = line.split(',').map(_.toDouble)
  (parts(0), parts(1), 1.0)
}
// Split data into training (60%) and test (40%) sets.
val splits = parsedData.randomSplit(Array(0.6, 0.4), seed = 11L)
val training = splits(0)
val test = splits(1)
// Create isotonic regression model from training data.
// Isotonic parameter defaults to true so it is only shown for demonstration
val model = new IsotonicRegression().setIsotonic(true).run(training)
// Create tuples of predicted and real labels.
val predictionAndLabel = test.map { point =>
  val predictedLabel = model.predict(point._2)
  (predictedLabel, point._1)
}
// Calculate mean squared error between predicted and real labels.
val meanSquaredError = predictionAndLabel.map { case (p, l) => math.pow((p - l), 2) }.mean()
println("Mean Squared Error = " + meanSquaredError)
```

### 4.2 训练过程分析

&emsp;&emsp;`parallelPoolAdjacentViolators`方法用于实现保序回归的训练。`parallelPoolAdjacentViolators`方法的代码如下：

```scala
private def parallelPoolAdjacentViolators(
      input: RDD[(Double, Double, Double)]): Array[(Double, Double, Double)] = {
    val parallelStepResult = input
      //以（feature，label）为key进行排序
      .sortBy(x => (x._2, x._1))
      .glom()//合并不同分区的数据为一个数组
      .flatMap(poolAdjacentViolators)
      .collect()
      .sortBy(x => (x._2, x._1)) // Sort again because collect() doesn't promise ordering.
    poolAdjacentViolators(parallelStepResult)
  }
```
&emsp;&emsp;`parallelPoolAdjacentViolators`方法的主要实现是`poolAdjacentViolators`方法，该方法主要的实现过程如下：

```scala
var i = 0
val len = input.length
while (i < len) {
     var j = i
     //找到破坏单调性的元祖的index
     while (j < len - 1 && input(j)._1 > input(j + 1)._1) {
       j = j + 1
     }
     // 如果没有找到违规点，移动到下一个数据点
     if (i == j) {
       i = i + 1
     } else {
       // 否则用pool方法处理违规的节点
       // 并且检查pool之后，之前处理过的节点是否违反了单调性约束
       while (i >= 0 && input(i)._1 > input(i + 1)._1) {
          pool(input, i, j)
          i = i - 1
       }
       i = j
     }
}
```

&emsp;&emsp;`pool`方法的实现如下所示。

```scala
def pool(input: Array[(Double, Double, Double)], start: Int, end: Int): Unit = {
      //取得i到j之间的元组组成的子序列
      val poolSubArray = input.slice(start, end + 1)
      //求子序列sum（label * w）之和
      val weightedSum = poolSubArray.map(lp => lp._1 * lp._3).sum
      //求权重之和
      val weight = poolSubArray.map(_._3).sum
      var i = start
      //子区间的所有元组标签相同，即拥有相同的预测
      while (i <= end) {
        //修改标签值为两者之商
        input(i) = (weightedSum / weight, input(i)._2, input(i)._3)
        i = i + 1
      }
}
```
&emsp;&emsp;经过上文的处理之后，`input`根据中的`label`和`feature`均是按升序排列。对于拥有相同预测的点，我们只保留两个特征边界点。

```scala
val compressed = ArrayBuffer.empty[(Double, Double, Double)]
var (curLabel, curFeature, curWeight) = input.head
var rightBound = curFeature
def merge(): Unit = {
    compressed += ((curLabel, curFeature, curWeight))
    if (rightBound > curFeature) {
        compressed += ((curLabel, rightBound, 0.0))
    }
}
i = 1
while (i < input.length) {
    val (label, feature, weight) = input(i)
    if (label == curLabel) {
       //权重叠加
       curWeight += weight
       rightBound = feature
    } else {//如果标签不同，合并
       merge()
       curLabel = label
       curFeature = feature
       curWeight = weight
       rightBound = curFeature
    }
    i += 1
}
merge()
```

&emsp;&emsp;最后将训练的结果保存为模型。

```scala
//标签集
val predictions = if (isotonic) pooled.map(_._1) else pooled.map(-_._1)
//特征集
val boundaries = pooled.map(_._2)
new IsotonicRegressionModel(boundaries, predictions, isotonic)
```

### 4.3 预测过程分析

```scala
def predict(testData: Double): Double = {
    def linearInterpolation(x1: Double, y1: Double, x2: Double, y2: Double, x: Double): Double = {
      y1 + (y2 - y1) * (x - x1) / (x2 - x1)
    }
    //二分查找index
    val foundIndex = binarySearch(boundaries, testData)
    val insertIndex = -foundIndex - 1
    // Find if the index was lower than all values,
    // higher than all values, in between two values or exact match.
    if (insertIndex == 0) {
      predictions.head
    } else if (insertIndex == boundaries.length){
      predictions.last
    } else if (foundIndex < 0) {
      linearInterpolation(
        boundaries(insertIndex - 1),
        predictions(insertIndex - 1),
        boundaries(insertIndex),
        predictions(insertIndex),
        testData)
    } else {
      predictions(foundIndex)
    }
  }
```
&emsp;&emsp;当测试数据精确匹配一个边界，那么返回相应的特征。如果测试数据比所有边界都大或者小，那么分别返回第一个和最后一个特征。当测试数据位于两个边界之间，使用`linearInterpolation`方法计算特征。
这个方法是线性内插法。
