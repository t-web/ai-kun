## XGBoost

> xgboost是大规模并行boosted tree的工具，它是目前最快最好的开源boosted tree工具包，比常见的工具包快10倍以上。在数据科学方面，有大量kaggle选手选用它进行数据挖掘比赛，其中包括两个以上kaggle比赛的夺冠方案。在工业界规模方面，xgboost的分布式版本有广泛的可移植性，支持在YARN, MPI, Sungrid Engine等各个平台上面运行，并且保留了单机并行版本的各种优化，使得它可以很好地解决于工业界规模的问题。

XGBoost是现在我们产品中主要使用的机器学习算法，应用在消费金融、现金贷等信贷的风控当中，做好坏人的分类问题；

### XGBoost原理

去说XGBoost之前，需要对很多知识有所了解，如下：

* 决策树 
* 提升方法 Boosting
* 提升树 Boosting tree
* 梯度提升树  Gradient Boosting
* GBDT

#### 提升方法 Boosting

提升方法(boosting)是一种常用的统计学习方法，在分类问题中，它通过改变训练样本的权重，学习多个分类器，并将这些分类器进行线性组合，提供分类的性能。boosting和bagging是集成学习中的基本算法，他们使用的多个分类器的类型是一致的。

> boosting是这样一种思想： 对于一个复杂任务来说，将多个专家的判定进行适当的综合得出的判断，要比其中任何一个专家单独的判断好。很容易理解，就是”三个臭皮匠顶个诸葛亮”的意思；
对于boosting有两个重要的概念：
* 弱分类器
一个概念（一个类，label），如果存在一个多项式的学习算法能够学习它，学习的正确率仅比随机猜测略好，那么就称这个概念是弱可学习的
* 强分类器
一个概念（一个类，label），如果存在一个多项式的学习算法能够学习它，并且正确率很高，那么就称这个概念是强可学习的；


提升方法面临的问题是：
> 一组“弱分类器”的集合能否生成一个“强分类器”？

对于分类问题, 提升方法的做法:
> * 从学习算法出发, 反复学习, 得到一系列弱分类器(又称基本分类器)
* 然后组合这些弱分类器, 构成一个强分类器

* bagging

bagging也叫自助汇聚法（bootstrap aggregating），比如原数据集中有N个样本，我们每次从原数据集中有放回的抽取，抽取N次，就得到了一个新的有N个样本的数据集，然后我们抽取S个N次，就得到了S个有N个样本的新数据集，然后拿这S个数据集去训练S个分类器，之后应用这S个分类器进行分类，选择分类器投票最多的类别作为最后的分类结果。一般来说自助样本的包含有63%的原始训练数据；

这样，在一次bootstrap的过程中，会有36%的样本没有被采样到，它们被称为out-off-bag(oob)，这是自助采样带给bagging的里一个优点，因为我们可以用oob进行“包外估计”out-of-bag estimate。

bagging通过降低基分类器的方差改善了泛化误差，bagging的性能依赖于基分类器的稳定性。如果基分类器是不稳定的，bagging**有助于减少训练数据的随机波动导致的误差，如果基分类器是稳定的，即对训练数据集中的微小变化是鲁棒的，则组合分类器的误差主要由基分类器偏移所引起的，这种情况下，**bagging可能不会对基分类器有明显的改进效果，甚至可能降低分类器的性能。

AdaBoost是boosting方法中最流行的版本；[有关boosting方法的概述看这里](https://baike.baidu.com/item/boosting/1403912)

Boosting 的两个问题：

> * 每一轮如何改变训练数据的权值或概率分布?
* 如何将弱分类器组合成一个强分类器?


##### AdaBoost

> AdaBoost（adaptive boosting）是元算法，通过组合多个弱分类器来构建一个强分类器。我们为训练数据中的每一个样本都赋予其一个权重，这些权重构成了向量D，一开始，这些权重都初始化成相等值，然后每次添加一个弱分类器对样本进行分类，从第二次分类开始，将上一次分错的样本的权重提高，分对的样本权重降低，持续迭代。此外，对于每个弱分类器而言，每个分类器也有自己的权重，取决于它分类的加权错误率，加权错误率越低，则这个分类器的权重值α越高，最后综合多个弱分类器的分类结果和其对应的权重α得到预测结果，AdaBoost是最好的监督学习分类方法之一。

总结一下AdaBoost的做法：

* 目的: 使错误分类样本的权值加大, 在后一轮的弱分类器中, 得到更多关注
  * 提高 前一轮弱分类器 错误分类样本 的权值
  * 降低 被 正确分类样本 的权值.
  
* 弱分类的组合: AdaBoost采用加权多数表决的方法.
  * 加大 分类误差率小的弱分类器的权值, 使其在表决中起较大的作用
  * 减小 分类误差率大的弱分类器的权值, 使其在表决中起较小的作用
  
算法思路：

> 

### XGBoost参考资料

* [维基百科-提升方法](https://zh.wikipedia.org/wiki/%E6%8F%90%E5%8D%87%E6%96%B9%E6%B3%95)
* [提升方法](https://clyyuanzi.gitbooks.io/julymlnotes/content/adaboost.html)
* [百科对boosting的起源介绍](https://baike.baidu.com/item/boosting/1403912)
* [对boostrap bagging boosting非常好的解释](https://lidongxuan.github.io/blog/boosting)
* [boosting家族的讲解](http://www.52caml.com/head_first_ml/ml-chapter6-boosting-family/)
* [XGBoost awesome demo](https://github.com/dmlc/xgboost/tree/master/demo)