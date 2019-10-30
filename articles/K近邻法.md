## K近邻及代码实现

k近邻(k-nearest neighbor,k-NN)十一中基本分类与回归方法，k近邻法假设给定一个训练数据集，其中实例类别一定，分类是对新的实例，根据其k个最近邻的训练实例的类别通过多说表决的方式进行预测。因此k近邻法 不具有显式的学习过程。

k近邻算法简单直观：给定一个训练数据集，对新的输入实例，在训练数据集中找到与该实例最近的k个实例，这k个实例的多数属于某个类，就把该输入实例分为这个类

### 算法流程

输入：训练数据集

$T=\begin{Bmatrix} (x_1,y_1),(x_2,y_2),...(x_N,y_N)\end{Bmatrix}$

其中$x_i \in R^n$为实例的特征向量，$y \in Y = \begin {Bmatrix}c_1,c_2,...,c_K \end{Bmatrix}$

输出：实例$x$所属的类$y$

1. 根据给定的距离度量，在训练集$T$中找出与$x$最近邻的$k$个点，涵盖这$k$个点的$x$的领域记作$N_k(x)$

2. 在$N_k{x}$中根据分类决策规则巨顶$x$的类别$y$

> 当k=1时成为最近邻算法

### 距离度量

特征空间中两个实例的距离是两个实例点相似度的反映，knn算法通常使用的距离是$L_p$距离,$x_i$和$x_j$的$L_p$距离如下：

$L_p(x_i,x_j)=(\sum_{l=1}^{n}|x_i^l-x_j^l|^p)^{\frac {1} {p}}$

当$p=1$时称为曼哈顿距离(Manhattan distance):

$L_1(x_i,x_j)=\sum_{l=1}^n|x_i^l-x_j^l|$

当$p=2$时称为欧氏距离(Euclidean distance):

$L_2(x_i,x_j)=\sum_{l=1}^n(|x_i^l-x_j^2|)^{\frac {1} {2}}$

### K值的选择

k值的选择会对k近邻法的结果产生重大影响。如果选择较小的值，就相当于用较小的领域中的训练实例进行预测，“学习”的近似误差会减少，只有与输入实例近似的训练实例才会对预测结果其作用。但缺点是估计误差会增大，如果邻近的实例点恰巧是噪声点，预测就会出错，k值的减少意味着整理模型变得复杂，容易过拟合，防止k值增大会是模型变得简单。

### 算法实现

假设有下图的例子

![](https://raw.githubusercontent.com/Hexmagic/store_image/master/2019/10/16-17-52-20-knn.png)

算法实现:

```python
import numpy as np
from collections import Counter


class KNeighbor(object):
    def __init__(self, n_neighbors=3, distance='euc'):
        '''
        distance: euc 欧几里得距离
                : man 曼哈顿距离
        '''
        self.n_neighbors = n_neighbors
        self.data = None
        if distance == 'euc':
            self.distance = self.edu_distance
        elif distance == 'man':
            self.distance = self.man_distance

    def edu_distance(self, a, b):
        return np.power(a - b, 2).sum()

    def man_distance(self, a, b):
        return np.abs(a - b).sum()

    def fit(self, X, y):
        X = np.array(X)
        y = np.array(y).reshape((-1, 1))
        self.data = np.hstack((X, y))

    def caculate_distance(self, x):
        rst = []
        for ele in self.data:
            X, y = ele[:-1], ele[-1]
            distance = self.distance(X, x)
            rst.append([distance, X, y])
        return rst

    def mode(self, lst):
        cnt = Counter(lst)
        return cnt.most_common(1)[0][0]

    def predict(self, x):
        support = self._support(x)
        return self.mode(ele[-1] for ele in support)

    def _support(self, x):
        distances = self.caculate_distance(x)
        sorted_distances = sorted(distances, key=lambda x: x[0])
        return sorted_distances[:self.n_neighbors]


X = [[0.58, 0.46], [0.54, 1.06], [1.2, 0.3], [1.42, 0.98], [0.9, 0.86],
     [0.36, 2.12], [0.36, 1.76], [1.3, 1.88], [0.92, 1.6], [1.14, 2.38],
     [1.86, 0.52], [2.38, 1.5], [1.48, 1.52], [1.88, 1.32], [2.36, 1.16],
     [2.6, 3.2], [2.52, 2.68], [1.54, 3.24], [1.76, 2.9], [1.88, 2.44],
     [3.34, 2.2], [2.38, 2], [2.8, 2.18], [1.86, 2], [1.46, 2.72],
     [0.48, 2.84], [0.96, 2.7], [3.32, 1.54], [2.86, 1.66], [2.76, 0.4],
     [2.78, 1.16], [2.62, 0.88], [2.26, 0.68]]

y = [0, 0, 0, 0, 0, 0, 0, 0, 0, 1, 0, 1, 0, 0, 0] + [1] * 18
knn = KNeighbor(distance='man', n_neighbors=3)
knn.fit(X, y)
rst = knn._support([1, 1.25])
```

查看预测结果和预测支撑点

![](https://raw.githubusercontent.com/Hexmagic/store_image/master/2019/10/16-17-53-15-knn_fit.png)

### KNN进阶

我们现在实现的knn效率比较低，因为每次预测都需要计算与所有点的距离。为了提高k近邻搜索的效率，可以考虑使用特俗的结构存储训练数据，减少计算距离的次数，也就是**kd树**，距离实现我这里暂不叙述，以后再补上。
