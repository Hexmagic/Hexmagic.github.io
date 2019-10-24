***

正确的判断来自于经验，而经验来自于错误的判断。 — Frederick P. Brooks

***

# 1 线性模型

线性模型（Linear Model）是机器学习中应用最广泛的模型，指通过样本特 征的线性组合来进行预测的模型。给定一个d维样本[x1,··· ,xd]T，其线性组合 函数为

$f(x;w)=w^{T}x+b$

其中w = [w1,··· ,wd]T 为d维的权重向量， b为偏置

在分类问题中，由于输出目标y 是一些离散的标签，而f(x;w)的值域为 实数，因此无法直接用f(x;w)来进行预测，需要引入一个非线性的决策函数 （Decision Function）g(.)来预测输出目标

$y=g(f(x;w))$

其中f(x;w)也称为判别函数（Discriminant Function）。

对于两类分类问题， g(·)可以是符号函数（Sign Function） 

![](https://raw.githubusercontent.com/Hexmagic/store_image/master/2019/09/28-10-36-17-2019-09-28-10-30-42-image.png)

# 2 判别函数和决策边界

，一个线性分类模型（Linear Classiﬁcation Model）或线 性分类器（Linear Classiﬁer），是由一个（或多个）线性的判别函数f(x;w) = wTx+ b和非线性的决策函数g(·)组成。我们首先考虑两类分类的情况，然后再 扩展到多类分类的情况。 

## 2.1 二分类

**二分类**（Binary Classiﬁcation）的类别标签y只有两种取值，通常可以设 为{+1,−1}。

在二分类问题中，我们只需要一个线性判别函数$f(x;w)=w^{T}x+b$。特征空间 Rd 中所有满足$f(x;w) = 0$的点组成用一个分割超平面（Hyperplane），称为决 策边界（Decision Boundary）或决策平面（Decision Surface）。决策边界将特征空间一分为二，划分成两个区域，每个区域对应一个类别

![](https://raw.githubusercontent.com/Hexmagic/store_image/master/2019/09/28-10-42-10-2019-09-28-10-41-43-image.png)

给定N 个样本的训练集$D ={(x^{(n)},y^{(n)})}^N_n=1$，其中$y(n) ∈{+1,−1}$，线性 模型试图学习到参数w∗，使得对于每个样本$(x^{(n)},y^{(n)})$尽量满足

$f(x^{(n)};w^*)>0 \; if \;y^{(n)} = 1$

$f(x^{(n)};w^*)<0 \; if \;y^{(n)} = -1$

两式合并，即参数$w^*$尽量满足

$y^{(n)}f(x^{(n)};w^*)>0, \; \forall n \in \left [1,N \right]$

对于训练集$D = \left \{x^{(n)},y^{(n)}\right\}^N_{n=1}$，如果存 在权重向量$w∗$，对所有样本都满足$yf(x;w^{∗}) > 0$，那么训练集D 是线 性可分的。

## 2.2 多分类

多类分类（Multi-class Classiﬁcation）问题是指分类的类别数C 大于2。多 类分类一般需要多个线性判别函数，但设计这些判别函数有很多种方式。 

1. “一对多”方式(One Vs Rest)：把多类分类问题转换为C 个“一对其余”的两类分类问 题。这种方式共需要C 个判别函数，其中第c个判别函数fc 是将类c的样本 和不属于类c的样本分开。
2. “一对一”方式(One Vs One)：把多类分类问题转换为C(C −1)/2个“一对一”的两类分 类问题。这种方式共需要C(C −1)/2个判别函数，其中第(i,j)个判别函数 是把类i和类j 的样本分开。1 ≤ i < j ≤ C 
3. “argmax”方式：这是一种改进的“一对其余”方式，共需要C 个判别函数

$f_c(x;wc)=wT cx+bc,\; \; \:   c =[1,··· ,C]$

如果存在类别c，对于所有的其他类别$\tilde {c} (\tilde{c} \neq c)$都满足$f_c(x;wc) > f_c(x,w˜ c)$， 那么x属于类别c。即

$y = \overset {C} {\underset {c=1} {argmax}} f_c(x;w_c)$

“一对其余”方式和“一对一”方式都存在一个缺陷：特征空间中会存在一些 难以确定类别的区域，而“argmax”方式很好地解决了这个问题。

![](https://raw.githubusercontent.com/Hexmagic/store_image/master/2019/09/28-11-14-12-2019-09-28-11-14-08-image.png)

**多线性可分**:对于训练集$D = \left \{x^{(n)},y^{(n)}\right\}^N_{n=1}$，如果存在$C$个权重向量$w^{*}_c,1 \leq c \leq C$,对于所有第c类样本满足$f_c(x;w_c)>f_c(x,w_{\tilde{c}}), \; \forall {\tilde{c}} \neq c$,那么训练集$D$线性可分

# 3 Logistic 回归

Logistic回归（Logistic Regression，LR）是一种常用的处理两类分类问题的 线性模型。在本节中我们采用y ∈{0,1}以符合Logistic回归的描述习惯

为了解决连续的线性函数不适合进行分类的问题，我们引入非线性函数$g : R^{d} →(0,1)$来预测类别标签的后验概率$p(y =1|x)$.

$p(y=1|x)=g(f(x;w))$

其中g(·)通常称为**激活函数（Activation Function）**，其作用是把线性函数的值域 从实数区间“挤压”到了(0,1)之间，可以用来表示概率

> 在Logistic回归中，我们使用Logistic函数来作为激活函数。这就是logistic回归名字的由来

Logistic回归采用交叉熵作为损失函数，并使用梯度下降法来对参数进行优化，这里不在赘述，参考[罗罗攀
:机器学习实战之Logistic回归](https://www.jianshu.com/p/96566542b07a)

# 4 Softmax回归

Softmax回归（Softmax Regression），也称为多项（multinomial）或多类 （multi-class）的Logistic回归，是Logistic回归在多类分类问题上的推广
