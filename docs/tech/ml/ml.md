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