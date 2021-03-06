# 一些概率统计的名词的理解

## 概率和统计

概率和统计是两个相反的过程，概率相当于机器学习中的预测，统计相当于机器学习中的训练或者建模。也就是说用统计来建模，在前者的基础上用概率来预测。

## 期望

期望是随机变量所有可能取值的加权平均，也就是平均取值。期望描述的是样本集合的中间点，这样的信息是有限的，所以引入标准差。标准差描述的是样本集合的各个样本点到均值的距离之平均。

## 方差

方差用来度量随机变量和其数学期望之间的偏离程度。
以这两个集合为例，[0，8，12，20]和[8，9，11，12]，两个集合的均值都是10，但显然两个集合差别是很大的，计算两者的标准差，前者是8.3，后者是1.8，显然后者较为集中，故其标准差小一些，标准差描述的就是这种“散布度”。之所以除以n-1而不是除以n，是因为这样能使我们以较小的样本集更好的逼近总体的标准差，即统计上所谓的“无偏估计”。而方差则仅仅是标准差的平方。

## 标准差(（Standard Deviation)

也称之为均方差(mean square error)
是各数据偏离平均数的距离的平均数，它是离均差平方和平均后的方根，用σ表示。标准差是方差的算术平方根。标准差能反映一个数据集的离散程度。平均数相同的，标准差未必相同

## 协方差

协方差用于衡量两个变量的总体误差。而方差是协方差的一种特殊情况，即当两个变量是相同的情况，也就是说标准差和方差一般是用来描述一维数据的，而协方差是用来描述两纬数据的，就是两个随机变量。
协方差的结果有什么意义呢？如果结果为正值，则说明两者是正相关的(从协方差可以引出“相关系数”的定义)，结果为负值就说明负相关的，如果为0，也是就是统计上说的“相互独立”。
这里需要说明一点：在计算期望是也有可能是两个随机变量，x和y，但是这两个随机变量是不同的样本，但是在一个纬度上，而协方差的x和y是两个纬度。
比如说需要统计期末考试的成绩，如果统计一个班的语文平均成绩，那么就只有一个随机变量x，而且是一个纬度，就是期望E(X)。
如果统计一班和二班的语文平均成绩，那么就是计算不同样本的期望，但是还是一个纬度，如果两个班的语文成绩是相互独立的，那么：E(XY) = E(x)E(Y)
如果统计一个班的语文成绩和数学成绩的关联，那么就是计算协方差，是两个纬度Cov(X,Y)。

## 协方差矩阵

既然协方差是计算两个纬度之间的关系，那么有n个随机向量，任何两个元素都可以得到一个协方差，那么最终就可以得到一个协方差矩阵。
理解协方差矩阵的关键就在于牢记它计算的是不同维度之间的协方差，而不是不同样本之间，拿到一个样本矩阵，我们最先要明确的就是一行是一个样本还是一个维度，才能走下去。

下面用图标来解释：

### 正相关

当 X, Y 的联合分布像下图那样时，我们可以看出，大致上有： X 越大  Y 也越大， X 越小  Y 也越小，这种情况，我们称为“正相关”。

### 负相关

当X, Y 的联合分布像下图那样时，我们可以看出，大致上有：X 越大Y 反而越小，X 越小 Y 反而越大，这种情况，我们称为“负相关”。

### 不相关

当X, Y  的联合分布像下图那样时，我们可以看出：既不是X  越大Y 也越大，也不是 X 越大 Y 反而越小，这种情况我们称为“不相关”。

### 总结

当X 与Y 正相关时，它们的分布大部分在区域（1）和（3）中，小部分在区域（2）和（4）中，所以平均来说，有E(X-EX)(Y-EY)>0 。
当 X与 Y负相关时，它们的分布大部分在区域（2）和（4）中，小部分在区域（1）和（3）中，所以平均来说，有(X-EX)(Y-EY)<0 。
当 X与 Y不相关时，它们在区域（1）和（3）中的分布，与在区域（2）和（4）中的分布几乎一样多，所以平均来说，有(X-EX)(Y-EY)=0 。
所以，我们可以定义一个表示X, Y 相互关系的数字特征，也就是协方差

cov(X, Y) = E(X-EX)(Y-EY)。

当 cov(X, Y)>0时，表明 X与Y 正相关；
当 cov(X, Y)<0时，表明X与Y负相关；
当 cov(X, Y)=0时，表明X与Y不相关。
这就是协方差的意义。

## 贝叶斯公式

先给出一个贝叶斯公式

也就是说贝叶斯公式可以表示为：Posteriorprobability∝Likelihood×Prior probability，因为分母不变，只是作为归一化。

我们现在把这个贝叶斯公式转换到机器学习中的概念。
假设我们现在的机器学习的任务就是：在给定训练数据D时，确定假设空间H中的最佳假设。H其实就是我们的机器学习的训练模型。
用P(h)表示在没有训练数据前假设h拥有的初始概率。P(h)被称为h的先验概率，这正好对应了我们的模型一般都是由一个初始值。先验概率反映了关于h是一正确假设的机会的背景知识如果没有这一先验知识，可以简单地将每一候选假设赋予相同的先验概率。类似地，P(D)表示训练数据D的先验概率，P(D|h)表示假设h成立时D的概率。机器学习中，我们关心的是P(h|D)，即给定D时h的成立的概率，称为h的后验概率。那么贝叶斯公式就可以转化为：p(h|D)=P(D|H) * P(H)/P(D)。
P(h|D)随着P(h)和P(D|h)的增长而增长，随着P(D)的增长而减少，即如果D独立于h时被观察到的可能性越大，那么D对h的支持度越小。

再用大白话来解释下这个公式：首先在训练模型前，我们会有一批数据，也就是P(D)，然后对这个模型有一个初始化的值，也就是P(H)，然后这个初始化模型的前提下预测新的数据，也就是P(D|H)，就是在模型H的前提下，测试出现这批数据D的可能性，然后就可以求出新的模型H，就是P(H|D)

## 先验概率(Prior probability)

先验概率仅仅依赖于主观上的经验估计，也就是事先根据已有的知识推断。

## 后验概率(Posterior probability)

后验的意思就是在已经得到一些信息后，求某一事件的概率，如果已经路上有一起交通事故，在这个前提下问堵车的概率就是后验概率，如果直接问这条路堵不堵就是先验概率。
后验概率是关于参数 θ 在给定的证据信息 X 下的概率： p(θ|x) ，从公式上看正好和似然函数相反。

## 似然函数 (likelihood function)

在数理统计概念中概率和似然差不多，都是说某种事件发生的可能性，但是在统计学中是有区分的。
先说下统计中概率和似然的区分。
概率：用于在已知一些参数的情况下，预测接下来的观测所得到的结果。
似然性：用于在已知某些观测所得到的结果时，对有关事物的性质的参数进行估计。
一般讲到似然函数直接给出如下公式，但是这个公式其实特别难以理解，首先公式前半段并不是一个概率，更不是条件概率，只是关于$\theta$的函数而已。公式的后半部分也不是什么条件概率，而只是概率而已。

$L(\theta|x) = P(x|\theta)$其实在离散分别的情况下，似然函数如下表示:${L}(\theta |x)=p_{\theta }(x)=P_{\theta }(X=x)$

$P_{\theta}(X=x)$经常写成：$P(X=x|\theta)$,又可以写成$P(X=x;\theta)$，所以并不是什么条件概率。在连续分布的情况下，似然函数表示成：$L(\theta|x) = f_{\theta}(x)$
$f_{\theta}(x)$又经常写成$f(x|\theta)$

## 矩(moment)

[moment](https://en.wikipedia.org/wiki/Moment_(mathematics))

$$\[1+\frac12+\frac13+\cdots+\frac1n=\ln n+\gamma+\varepsilon_n\\
1+\frac{1}{2^2}+\frac{1}{3^2}+\cdots=\frac{\pi^2}{6}\\
\mathbb{E}(S^2)=\sigma^2\\
\lim_{n\to\infty}\mathbb{P}\left(\frac{X_1+X_2+\cdots+X_n-n\mu}{\sigma\sqrt n}\leqslant y\right)=\Phi(y)=\frac{1}{\sqrt{2\pi}}\int_{-\infty}^y \exp\{-\frac{t^2}{2}\}\mathrm dt
\]$$
