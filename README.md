- [ 机器学习经验整理](#head1)
- [ 机器学习模型中的两类参数](#head2)
- [ 参数和超参数的区别](#head3)
- [ 哪些属于超参数](#head4)
- [ torch.randn()](#head5)
- [计算机视觉模型库-Pytorch Image Models (timm)](#head6)
- [ BCELoss和BCEWithLogitsLoss](#head7)
- [ k-交叉验证KFold](#head8)
- [*args 与 **kwargs 的区别，两者都是 python 中的可变参数](#head9)
- [ KFlod取值问题](#head10)
# <span id="head1"> 机器学习经验整理</span>

(拒绝拖延!!!)

# <span id="head2"> 机器学习模型中的两类参数</span>

一类需要从数据中学习和估计得到，称为模型参数（Parameter）---即模型本身的参数。比如，线性回归直线的加权系数（斜率）及其偏差项（截距）都是模型参数。
还有一类则是机器学习算法中的调优参数（tuning parameters），需要人为设定，称为超参数（Hyperparameter）。比如，正则化系数λ，决策树模型中树的深度。

# <span id="head3"> 参数和超参数的区别</span>

模型参数是模型内部的配置变量，需要用数据估计模型参数的值
模型超参数是模型外部的配置，需要手动设置超参数的值。
机器学习中一直说的“调参”，实际上不是调“参数”，而是调“超参数”。

# <span id="head4"> 哪些属于超参数</span>

梯度下降法中的学习速率α，迭代次数epoch，批量大小batch-size，k近邻法中的k（最相近的点的个数），决策树模型中树的深度，等等。

# <span id="head5"> torch.randn()</span>

用来生成随机数字的tensor，这些随机数字满足标准正态分布（0~1）。
torch.randn（size),size可以是一个整数，也可以是一个元组。
代码示例：

```
import torch  
a=torch.randn(3)  
b=torch.randn(3,4)  
print("a:",a)  
print("b:",b)
```

```
a: tensor([ 0.9405, -0.1068,  0.1712])
b: tensor([[-1.0962, -0.1893,  1.2323,  0.5703],
        [-1.5256, -1.4923,  0.4275,  0.5143],
        [ 1.1200,  0.5317,  1.1961, -2.2533]])
```

# <span id="head6">计算机视觉模型库-Pytorch Image Models (timm)</span>

"timm"是由Ross Wightman创建的深度学习库，是一个关于SOTA的计算机视觉模型、层、实用工具、optimizers, schedulers, data-loaders, augmentations，可以复现ImageNet训练结果的训练/验证代码。
https://fastai.github.io/timmdocs/

#jupyer的感叹号!用于执行来自操作系统的命令

# <span id="head7"> BCELoss和BCEWithLogitsLoss</span>

![](img/img1.png)![](img/img2.png)
BCEWithLogitsLoss函数包括了 Sigmoid 层和 BCELoss 层. 适用于多标签分类任务

CrossEntropyLoss函数 包含Softmax,层和 NLLLoss层,适用于单标签分类问题

https://blog.csdn.net/qq_22210253/article/details/85222093

# <span id="head8"> k-交叉验证KFold</span>

KFold 是 sklearn 包中用于交叉验证的函数。在机器学习中，样本量不充足时，通常使用交叉训练验证。
https://www.cnblogs.com/loubin/p/11305565.html
原理补充：

在机器学习建模过程中，通行的做法通常是将数据分为训练集和测试集。测试集是与训练独立的数据，完全不参与训练，用于最终模型的评估。在训练过程中，经常会出现过拟合的问题，就是模型可以很好的匹配训练数据，却不能很好在预测训练集外的数据。如果此时就使用测试数据来调整模型参数，就相当于在训练时已知部分测试数据的信息，会影响最终评估结果的准确性。通常的做法是在训练数据再中分出一部分做为验证(Validation)数据，用来评估模型的训练效果。

验证数据取自训练数据，但不参与训练，这样可以相对客观的评估模型对于训练集之外数据的匹配程度。模型在验证数据中的评估常用的是交叉验证，又称循环验证。它将原始数据分成K组(K-Fold)，将每个子集数据分别做一次验证集，其余的K-1组子集数据作为训练集，这样会得到K个模型。这K个模型分别在验证集中评估结果，最后的误差MSE(Mean Squared Error)加和平均就得到交叉验证误差。交叉验证有效利用了有限的数据，并且评估结果能够尽可能接近模型在测试集上的表现，可以做为模型优化的指标使用。

**疑问解答:**

* 为什么不直接拆分训练集与数据集，来验证模型性能，反而采用多次划分的形式，岂不是太麻烦了？

> 我们为了防止在训练过程中，出现过拟合的问题，通行的做法通常是将数据分为训练集和测试集。测试集是与训练独立的数据，完全不参与训练，用于最终模型的评估。这样的直接划分会导致一个问题就是测试集不会参与训练，这样在小的数据集上会浪费掉这部分数据，无法使模型达到最优（数据决定了程性能上限，模型与算法会逼近这个上限）。但是我们又不能划分测试集，因为需要验证网络泛化性能。采用K-Fold 多次划分的形式就可以利用全部数据集。最后采用平均的方法合理表示模型性能。

* 为什么还要进行所有数据集重新训练，是否太浪费时间？

> 我们通过K-Fold 多次划分的形式进行训练是为了获取某个模型的性能指标，单一K-Fold训练的模型无法表示总体性能，但是我们可以通过K-Fold训练的训练记录下来较为优异的超参数，然后再以最优模型最优参数进行重新训练，将会取得更优结果。 也可以采取方法一的方式不再进行训练使用模型融合的方式。

# <span id="head9">*args 与 **kwargs 的区别，两者都是 python 中的可变参数</span>

*args 和**kwargs可以写成任意形式
*args 表示任何多个无名参数，它本质是一个 tuple
**kwargs 表示关键字参数，它本质上是一个 dict
如果同时使用 *args 和 **kwargs 时，必须 *args 参数列要在 **kwargs 之前。

```
>>>def fun(*args, **kwargs):
print('args=', args)
print('kwargs=', kwargs)
```

```
>>>fun(1, 2, 3, 4, A='a', B='b', C='c', D='d')
args= (1, 2, 3, 4)
kwargs= {'A': 'a', 'B': 'b', 'C': 'c', 'D': 'd'}
```

# <span id="head10"> KFlod取值问题</span>

运用Kfold交叉验证时，在一个限度内k的值越大越好。因为k越大我们验证的次数就越多，最后取出来的平均数越能代表训练模型的准确度。

但是k是需要在一个限度之内的。k太大有两个坏处。

* 容易给机器造成过重负担，花费大量时间。
* 每一次验证的测试集（或验证集）中数据太少，很难得到准确的误报率。

总体而言，k一般取10，取值依不同项目情况而定，当然一定存在k<n（训练集数据条数）。

